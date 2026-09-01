# 監視指標リファレンス

[← ShimoDocs Suite デプロイメント文書](../README.md)

この文書は、監視システムで一般的に使用される指標を整理しており、ノード、containerdコンテナをカバーしています。 Kubernetes クラスター、ミドルウェア、およびアプリケーションサービスに関する情報を提供し、日常点検、容量評価、およびトラブルシューティングの統一リファレンスを提供します。 

メトリック名は、Prometheusで収集された実際のエクスポーターのメトリックに基づいています。エクスポーターのバージョンによっては若干の違いがある場合があり、実際のトラブルシューティングはオンラインクエリ結果を最終参照として行う必要があります。 

## 対象範囲 

| カテゴリ | 対象オブジェクト | 
| --- | --- | 
| ノード監視 | Linuxホスト、システムリソース、ディスク、ネットワーク、プロセス | 
| コンテナ監視 | containerdで実行されているコンテナ、Podコンテナのリソース | 
| Kubernetes クラスター | ノード、Pod、Deployment、StatefulSet、Job、 PVC、APIServer | 
| MySQL | MySQL インスタンス、接続、クエリ、キャッシュ、ロック、ネットワーク | 
| MongoDB | MongoDB インスタンス、接続、操作、メモリ、ネットワーク、レプリケーションバッファ | 
| Redis | Redis インスタンス、クライアント、コマンド、メモリ、キー スペース、ヒット率 | 
| Kafka | ブローカー、トピック、パーティション、コンシューマーグループ、遅延、レプリカ | 
| MinIO | クラスタノード、ディスク、バケット S3 リクエスト、オブジェクト容量 | 
| Elasticsearch | クラスタの状態、ノード、シャード、インデックス JVMスレッドプール、ネットワーク |
| アプリケーションサービス | 一般的なサーバー、クライアントコール、共同編集、RSサービス、ランタイム |

## メトリック読み取りルール

| メトリックタイプ | 読み取り方法 | 共通の PromQL 構文 | 説明 |
| --- | --- | --- | --- |
| カウンター | 時間ウィンドウ内の成長率または増分を見る | `rate(x_total[5m])`, `increase(x_total[5m])` | リクエスト数、エラー数、バイト数、IO回数は一般的にカウンターに属する |
| ゲージ | 現在の値、平均値、最大値を確認する | `avg(x)`, `max(x)`, `sum(x)` | メモリ、接続数、容量、ステータス値は一般的にゲージに属する |
| ヒストグラム | パーセンタイルレイテンシを確認する | `histogram_quantile(0.95, sum(rate(x_bucket[5m])) by (le))` | リクエストレイテンシ、処理レイテンシ、キューレイテンシは一般的にヒストグラムを使用する |
| 比率 | 割合を確認する | `A / B * 100` | 使用率、エラー率、ヒット率はすべて比率型メトリクスに属する |

閾値として固定数値を直接コピーすることは推奨されない。メトリクスには例えば CPUメモリ、ディスク、接続数 QPSおよび遅延などがあり、これらはビジネスのピーク時、容量計画、過去の基準と照らして評価する必要がある。ドキュメント内の異常動作はリスクを迅速に特定するために使用され、最終的なアラート閾値と同等ではない。

## 1. ノードサービス監視

ノード監視は、ホストが正常であるか、リソースが十分であるか、ディスクやネットワークのボトルネックがあるかを判断するために使用されます。ノードのメトリクスは主にnode-exporterから取得され、プロセスレベルでの特定を行うためにシステムプロセスダッシュボードと組み合わせて使用されます。

### 1.1 基本ステータス

| 監視次元 | メトリック | メトリクスの意味 | 一般的な標準/単位 | 異常なパフォーマンス |
| --- | --- | --- | --- | --- |
| ノード稼働状況 | `up` | エクスポーターまたは収集対象にアクセス可能か | `1` 収集可能を示す `0` 収集不可能を示す | 連続的 `0` ノード、ネットワーク、またはエクスポーターに問題があることを示す |
| 起動時間 | `node_boot_time_seconds` | ノードの最終起動時間 | Unixタイムスタンプ | ブート時間の変化は、ノードが再起動されたことを示します |
| ノード情報 | `node_uname_info`, `node_os_info` | オペレーティングシステム、カーネル、ディストリビューション情報 | ラベル情報 | ノードのバージョン確認に使用され、アラート指標として直接使用されることはありません |

トラブルシューティングの提案: まず確認してください `up` 次に `node_boot_time_seconds`. ノードが収集不可能で、ブート時間が最近変わった場合は、ホストの再起動、ネットワーク ACL、および node-exporter プロセスのステータスの確認を優先してください。

### 1.2 CPU メトリクス

| 監視次元 | メトリック | メトリクスの意味 | 一般的な標準/単位 | 異常なパフォーマンス |
| --- | --- | --- | --- | --- |
| CPU 使用状況 | `node_cpu_seconds_total` | 各コアが異なるモードで費やした累積時間 CPU 各コアが異なるモードで費やした時間 | パーセンテージ | `user` および `system` 長期的に高い場合、ノードの計算能力は逼迫しています |
| アイドル CPU | `node_cpu_seconds_total{mode="idle"}` | CPU アイドル時間 | パーセンテージ | アイドル時間が持続的に低い場合、キューイングやレイテンシの増加が発生する可能性があります |
| IO 待機 | `node_cpu_seconds_total{mode="iowait"}` | 時間 CPU ディスクIOを待っています | パーセンテージ | iowaitの継続的な増加は通常、ディスクまたはストレージリンクの遅さを示します |
| システム負荷 | `node_load1`, `node_load5`, `node_load15` | 1/5/15分間の平均負荷 | 負荷値 | コア数を常に上回るロードは、 CPU 目立つタスク待ち行列を示します |
| CPU 圧力 | `node_pressure_cpu_waiting_seconds_total` | 累積 CPU PSI 待機時間 | 秒/秒 | CPU リソースの競合が重要で、プロセスが待機しています CPU スケジューリング |

よくある質問:

```promql
100 - avg by (instance) (rate(node_cpu_seconds_total{mode="idle"}[5m])) * 100
```

```promql
avg by (instance) (rate(node_cpu_seconds_total{mode="iowait"}[5m])) * 100
```

調査の提案: いつ CPU の使用率が高い場合、まず以下の点を区別してください。 `user`, `system`、および `iowait`：高い `user` は主にビジネス計算の負荷によるもので、高い `system` はシステムコールやネットワークパケット処理に関連している可能性があり、高い `iowait` はディスクのスループット、 IOPSおよびレイテンシを確認する必要があります。

### 1.3 メモリ指標

| 監視次元 | メトリック | メトリクスの意味 | 共通単位 | 異常な現れ |
| --- | --- | --- | --- | --- |
| 総メモリ | `node_memory_MemTotal_bytes` | ノードの総物理メモリ | バイト | 使用率計算用 |
| 使用可能メモリ | `node_memory_MemAvailable_bytes` | システムがプロセスに割り当て可能なメモリ | バイト / パーセンテージ | 使用可能メモリが一貫して低いとトリガーされやすい OOM または頻繁な回収 |
| 空きメモリ | `node_memory_MemFree_bytes` | 完全に未使用のメモリ | バイト | Linuxではメモリ圧力を判断するために単独で使用できない |
| メモリ圧力 | `node_pressure_memory_waiting_seconds_total` | 累積メモリ PSI 待機時間 | 秒 / 秒 | メモリ回収または割り当て待機の増加 |
| OOM カウント | `node_vmstat_oom_kill` | システムによる数 OOM キル | カウント / 増分 | 増加した場合、終了したプロセスとメモリのピークを特定してください |

よくある質問:

```promql
(1 - node_memory_MemAvailable_bytes / node_memory_MemTotal_bytes) * 100
```

```promql
increase(node_vmstat_oom_kill[10m])
```

調査の提案: メモリのためにだけを見るのではなく `MemFree` 実際の利用可能性は、より多く `MemAvailable`と、コンテナの作業セットメモリ、プロセスの RSS、および OOM 記録と組み合わせて評価する必要があります。

### 1.4 ディスク容量とインデックスノード（Inodes）

| 監視次元 | メトリック | メトリクスの意味 | 一般的な測定/単位 | 異常なパフォーマンス |
| --- | --- | --- | --- | --- |
| ファイルシステム全体 | `node_filesystem_size_bytes` | マウントポイントの総容量 | バイト | ディスク使用率を計算するために使用されます |
| 使用可能なファイルシステム | `node_filesystem_avail_bytes` | 通常ユーザーが使用できる空きスペース | バイト | 使用可能スペースが不足すると書き込み失敗が発生する可能性があります |
| フリーファイルシステム | `node_filesystem_free_bytes` | ファイルシステム内の空きスペース | バイト | ルート予約スペースを含む; 通常、以下と一緒に考慮されます `avail` |
| 読み取り専用ステータス | `node_filesystem_readonly` | ファイルシステムが読み取り専用かどうか | `0/1` | ~の場合 `1`、業務書き込みは失敗します |
| 合計inode | `node_filesystem_files` | ファイルシステム内のinodeの総数 | カウント | 小さいファイルシナリオでは特に注意が必要です |
| 残りのinode | `node_filesystem_files_free` | 残りのinodeの数 | カウント/割合 | inodeが枯渇すると、ディスクスペースがまだあってもファイルを作成できません |

よくある質問:

```promql
(1 - node_filesystem_avail_bytes{fstype!~"tmpfs|overlay"} / node_filesystem_size_bytes{fstype!~"tmpfs|overlay"}) * 100
```

```promql
(1 - node_filesystem_files_free / node_filesystem_files) * 100
```

調査の提案: ディスク容量のアラートはマウントポイントごとに確認する必要があります。特にデータディスク、ログディスク、コンテナランタイムディレクトリに注意してください。高inode使用率は通常、多数の小さいファイル、ログスライス、または掃除されていない一時ファイルから生じます。

### 1.5 ディスク IOPS、スループット、およびレイテンシ

| 監視次元 | メトリック | メトリクスの意味 | 一般的な測定/単位 | 異常なパフォーマンス |
| --- | --- | --- | --- | --- |
| 読み取り IOPS | `node_disk_reads_completed_total` | 完了したディスク読み取り要求の数 | 回/秒 | 読み取り IOPS デバイス制限に近づくと、読み取り待機時間が増加します |
| 書き込み IOPS | `node_disk_writes_completed_total` | 完了したディスク書き込み要求の数 | 回/秒 | 書き込みバックログ、ログまたはデータベースのコミットが遅くなる |
| 読み取りスループット | `node_disk_read_bytes_total` | ディスクから読み取られた累積バイト数 | バイト/秒 | 高スループットおよび高iowaitはストレージが忙しいことを示します |
| 書き込みスループット | `node_disk_written_bytes_total` | ディスクに書き込まれた累積バイト数 | バイト/秒 | 長期間の高い書き込みスループットはデータベースやオブジェクトストレージに影響を与える可能性があります |
| 読み取り時間 | `node_disk_read_time_seconds_total` | 読み取り要求の累積時間 | 秒/秒 | 読み取り待機時間の増加 |
| 書き込み時間 | `node_disk_write_time_seconds_total` | 書き込み要求の累積時間 | 秒/秒 | 書き込み待機時間の増加 |
| IOが忙しい | `node_disk_io_time_seconds_total` | ディスクがIO処理に費やす累積時間 | パーセンテージ | ほぼフルロード時、アプリケーションはIOを待機する |
| 加重IO時間 | `node_disk_io_time_weighted_seconds_total` | キュー長を考慮したIO時間 | 秒/秒 | キューの蓄積は深刻なデバイスキューを示す |
| IOプレッシャー | `node_pressure_io_waiting_seconds_total` | 累積IO PSI 待機時間 | 秒/秒 | プロセスはIO待機により長時間費やす |

よくあるクエリ:

```promql
rate(node_disk_reads_completed_total[5m])
```

```promql
rate(node_disk_writes_completed_total[5m])
```

```promql
rate(node_disk_read_bytes_total[5m])
```

```promql
rate(node_disk_written_bytes_total[5m])
```

```promql
rate(node_disk_io_time_seconds_total[5m]) * 100
```

```promql
rate(node_disk_read_time_seconds_total[5m]) / rate(node_disk_reads_completed_total[5m])
```

```promql
rate(node_disk_write_time_seconds_total[5m]) / rate(node_disk_writes_completed_total[5m])
```

調査の提案: 問題を確認する際にディスク容量だけを見ないこと。容量が正常でも、 IOPSスループット、IOビジー、iowaitが同時に増加すると業務パフォーマンスは低下する可能性がある。データベースなどの重いIOサービスは、 Kafka、および MinIO 書き込み遅延とキューに注目するべきである。

### 1.6 ネットワーク指標

| 監視次元 | メトリック | メトリクスの意味 | 共通単位 | 異常な兆候 |
| --- | --- | --- | --- | --- |
| 受信トラフィック | `node_network_receive_bytes_total` | ネットワークカードが受信した累積バイト数 | バイト/秒 | 受信トラフィックの急増、リクエストのスパイクやデータ同期が原因の可能性あり |
| 送信トラフィック | `node_network_transmit_bytes_total` | ネットワークカードが送信した累積バイト数 | バイト/秒 | 送信トラフィックの急増、ダウンロード、バックアップ、またはレプリケーションが原因の可能性あり |
| 受信エラー | `node_network_receive_errs_total` | 受信エラーパケットの累積数 | カウント/秒 | ネットワークカード、リンク、またはドライバーの問題 |
| 送信エラー | `node_network_transmit_errs_total` | 送信エラーパケットの累積数 | カウント/秒 | リンクの問題、またはネットワークカードのキューの問題 |
| 受信パケットロス | `node_network_receive_drop_total` | 受信ドロップパケットの累積数 | カウント/秒 | カーネルキューまたはネットワークカードが追いつかない |
| 送信パケットロス | `node_network_transmit_drop_total` | 送信パケット損失の累積値 | 回/秒 | 出口の輻輳または NIC キューの圧力 |

よくあるクエリ:

```promql
rate(node_network_receive_bytes_total{device!~"lo|veth.*|cni.*"}[5m])
```

```promql
rate(node_network_transmit_bytes_total{device!~"lo|veth.*|cni.*"}[5m])
```

```promql
rate(node_network_receive_drop_total[5m]) + rate(node_network_transmit_drop_total[5m])
```

調査の提案: ネットワークの異常については、トラフィック、エラーパケット、およびパケット損失を一緒に確認してください。トラフィックが多いだけでは必ずしも障害を示すわけではありません。エラーパケットやパケット損失を伴うトラフィックの増加は、リンクまたはホストのネットワークスタックの問題である可能性が高いです。

### 1.7 TCP、ファイルハンドル、およびシステムストレス

| 監視次元 | メトリック | メトリクスの意味 | 共通単位 / 測定 | 異常な動作 |
| --- | --- | --- | --- | --- |
| 現在 TCP 接続 | `node_netstat_Tcp_CurrEstab` | 現在の確立済み接続数 TCP 接続 | 数 | 接続の急増は、トラフィックピークまたは接続リークを示す可能性があります |
| TIME_WAIT | `node_sockstat_TCP_tw` | 数 TIME_WAIT 接続 | 数 | 短期間で多くの接続が発生すると、ポートが枯渇したりカーネルの負荷が増加する可能性があります |
| TCP 割り当て済み | `node_sockstat_TCP_alloc` | 割り当てられた数 TCP ソケット | 数 | ソケットの数が継続的に増加している場合、接続解放の調査が必要です |
| TCP 使用中 | `node_sockstat_TCP_inuse` | 数 TCP 使用中のソケット | 数 | 接続圧力の増加 |
| TCP 孤立 | `node_sockstat_TCP_orphan` | 孤立ソケットの数 | 数 | 異常な増加は、異常な接続終了に関連している可能性があります |
| 使用中のファイルハンドル | `node_filefd_allocated` | システムによって割り当てられたファイルハンドルの数 | 個 | 数が高すぎると、新規接続やファイルオープンに影響する可能性があります |
| ファイルハンドル制限 | `node_filefd_maximum` | システムのファイルハンドル制限 | 個 | ハンドル使用率を計算するために使用されます |

よくある問い合わせ: 

```promql
node_filefd_allocated / node_filefd_maximum * 100
```

調査の推奨: ファイルハンドルおよび TCP 接続は通常一緒に考慮されます。サーバー接続の数が急増した場合、システムのハンドルが限界に近いと、アプリケーションで accept エラー、ファイルオープンエラー、または依存接続のエラーが発生する可能性があります。

### 1.8 プロセス監視

| 監視次元 | メトリック | メトリクスの意味 | 一般的な測定/単位 | 異常な動作 |
| --- | --- | --- | --- | --- |
| プロセス CPU | `process_cpu_seconds_total` | 合計 CPU プロセスの時間 | 秒/秒 | 長期間の高い CPU 単一プロセスによる使用 |
| 物理メモリ | `process_resident_memory_bytes` | プロセス RSS メモリ | バイト | 継続的な増加 RSS メモリリークを示している可能性があります |
| 仮想メモリ | `process_virtual_memory_bytes` | プロセスの仮想メモリ | バイト | 異常な増加は次と併せて評価する必要があります RSS |
| オープンハンドル | `process_open_fds` | プロセスのオープンファイルハンドルの数 | 数 | 継続的な増加はハンドルリークを示している可能性があります |
| 最大ハンドル数 | `process_max_fds` | プロセスが開くことのできるファイルハンドルの最大数 | 数 | プロセスのハンドル使用率を計算するために使用 |
| プロセス開始時刻 | `process_start_time_seconds` | プロセス開始時刻 | Unixタイムスタンプ | 開始時刻の変化はプロセスの再起動を示す |

調査の推奨事項: プロセスメトリクスは、ノードレベルの問題に対する特定のサービスを特定するために使用される。ノード CPU が高い場合、プロセスを確認 CPU; ノードのメモリ圧力が高い場合、確認 RSS; ノードのハンドルが高い場合、確認 `process_open_fds`. 

## 2. containerd コンテナ監視

コンテナ監視は主に kubelet/cAdvisor から提供され、containerd によって管理されるコンテナのリソース使用状況を反映する。文書は引き続き使用する `container_*` Prometheusメトリクスからのネーミングですが、実際の運用中の基盤コンテナランタイムはcontainerdです。 

### 2.1 コンテナ CPU

| 監視次元 | メトリック | メトリクスの意味 | 共通スコープ/単位 | 異常なパフォーマンス |
| --- | --- | --- | --- | --- |
| CPU 使用状況 | `container_cpu_usage_seconds_total` | 合計 CPU コンテナの使用時間 | コア/秒 | 長時間にわたり制限に近い使用率、ビジネス遅延の可能性 |
| CPU スロットル時間 | `container_cpu_cfs_throttled_seconds_total` | 合計時間 CPU によってスロットルされる CFS | 秒/秒 | 重要 CPU スロットリングは制限が厳しすぎるか、負荷が高すぎることを示す |
| CPU クオータ | `container_spec_cpu_quota` | コンテナ CPU クォータ | クォータ値 | 設定されているかを認識するために使用 CPU 制限が設定されているか |

よくある問い合わせ: 

```promql
sum by (namespace, pod, container) (rate(container_cpu_usage_seconds_total{container!="",image!=""}[5m]))
```

```promql
sum by (namespace, pod, container) (rate(container_cpu_cfs_throttled_seconds_total{container!="",image!=""}[5m]))
```

調査推奨: 高いコンテナ CPU は必ずしもスケーリングを必要とするわけではありません。まず、スロットリングされていないか確認し、次にPodのリクエスト/制限が低すぎないかを確認し、最後にサービスリクエストの遅延を考慮して、本当にビジネスに影響するかを判断してください。

### 2.2 コンテナメモリ

| 監視次元 | メトリック | メトリクスの意味 | 共通単位 | 異常な動作 |
| --- | --- | --- | --- | --- |
| RSS メモリ | `container_memory_rss` | コンテナの匿名ページおよび RSS メモリ | バイト | 継続的な増加は実際のプロセスメモリプレッシャーに近い |
| 使用メモリ | `container_memory_usage_bytes` | コンテナの総メモリ使用量 | バイト | キャッシュを含むため、単独ではリークを判断できない |
| ワーキングセットメモリ | `container_memory_working_set_bytes` | コンテナのアクティブなワーキングセットメモリ | バイト | 制限に近づくとOOMKilledの原因になる可能性がある |
| メモリ制限 | `container_spec_memory_limit_bytes` | コンテナメモリ制限 | バイト | メモリ使用率を計算するために使用 |

よくあるクエリ:

```promql
container_memory_working_set_bytes{container!="",image!=""} / container_spec_memory_limit_bytes{container!="",image!=""} * 100
```

調査の提案: ビジネスコンテナのメモリリスクについては、作業セットの確認を優先してください。また、 RSS. `usage_bytes` ページキャッシュの影響を大きく受けるため、容量観測には適していますが、判断の唯一の根拠としては適していません。 OOM 

### 2.3 コンテナのディスクと一時ストレージ

| 監視次元 | メトリック | メトリクスの意味 | 一般的な測定/単位 | 異常なパフォーマンス |
| --- | --- | --- | --- | --- |
| 読み取りスループット | `container_fs_reads_bytes_total` | コンテナによるディスクからの累積読み取りバイト数 | バイト/秒 | 読み取りトラフィックの急増は、スキャン、インポート、またはキャッシュ元プルを示唆する可能性があります |
| 書き込みスループット | `container_fs_writes_bytes_total` | コンテナによるディスクへの累積書き込みバイト数 | バイト/秒 | 書き込みピークはノードのIO負荷を引き起こす可能性があります |
| 読み取り IOPS | `container_fs_reads_total` | コンテナによる読み取りリクエスト数 | Ops/s | 小さいブロックの読み取り頻度が高いと、IO待機時間が増加する可能性があります |
| 書き込み IOPS | `container_fs_writes_total` | コンテナによる書き込みリクエスト数 | Ops/s | ログや一時ファイルの過剰な書き込み |
| ファイルシステム使用状況 | `container_fs_usage_bytes` | コンテナのファイルシステム使用状況 | バイト | 一時ファイルやログの蓄積 |
| ファイルシステムの制限 | `container_fs_limit_bytes` | コンテナのファイルシステム制限 | バイト | 制限に近づくと書き込みが失敗する可能性があります |

よくあるクエリ: 

```promql
sum by (namespace, pod, container) (rate(container_fs_reads_bytes_total{container!="",image!=""}[5m]))
```

```promql
sum by (namespace, pod, container) (rate(container_fs_writes_bytes_total{container!="",image!=""}[5m]))
```

調査の提案: コンテナのディスク書き込みが異常な場合は、まずPodのログボリューム、一時ファイルディレクトリ、およびバッチタスクを確認してください。ノードのディスクIOが高い場合は、コンテナFSメトリクスを使用してどのPodが書き込んでいるかを特定できます。

### 2.4 コンテナネットワーク

| 監視次元 | メトリック | メトリクスの意味 | 共通スコープ/単位 | 異常なパフォーマンス |
| --- | --- | --- | --- | --- |
| 着信トラフィック | `container_network_receive_bytes_total` | コンテナが受信した総バイト数 | バイト/秒 | リクエストトラフィックやレプリケーショントラフィックの急増 |
| 発信トラフィック | `container_network_transmit_bytes_total` | コンテナが送信した総バイト数 | バイト/秒 | ダウンロード、同期、オリジンフェッチ、またはエクスポートのトラフィックの増加 |
| 受信パケット損失 | `container_network_receive_packets_dropped_total` | コンテナが受信時に破棄した総パケット数 | 回/秒 | ネットワークスタックまたはノードのプレッシャーによるパケット損失 |
| 送信パケット損失 | `container_network_transmit_packets_dropped_total` | コンテナが送信時に破棄した総パケット数 | 回/秒 | 送信輻輳 NIC キュー、または CNI 問題 |

よくあるクエリ:

```promql
sum by (namespace, pod) (rate(container_network_receive_bytes_total[5m]))
```

```promql
sum by (namespace, pod) (rate(container_network_transmit_bytes_total[5m]))
```

調査の提案: コンテナネットワークはノードと連携して分析する必要があります NIC メトリクス。パケット損失がPodレベルで増加しているが、ノードに異常がない場合は、確認を続けてください CNI、iptables、およびPodが存在するノードの負荷。 

### 2.5 コンテナのスレッドとライフサイクル

| 監視次元 | メトリック | メトリクスの意味 | 共通スコープ/単位 | 異常な動作 |
| --- | --- | --- | --- | --- |
| スレッドの数 | `container_threads` | コンテナ内のスレッド数 | 数 | スレッドの継続的な増加はスレッドリークを示す可能性があります |
| 最終確認 | `container_last_seen` | cAdvisorによってコンテナが最後に確認された時間 | Unixタイムスタンプ | 長時間更新がない場合は、コンテナが終了したか収集の異常を示す可能性があります |
| 再起動回数 | `kube_pod_container_status_restarts_total` | コンテナの再起動の総数 | カウント/増加 | 頻繁な再起動はクラッシュ、プローブの失敗、または OOM |
| 待機理由 | `kube_pod_container_status_waiting_reason` | コンテナが待機状態にある理由 | ラベル値 | `CrashLoopBackOff`, `ImagePullBackOff`など、対応が必要です |
| 実行状況 | `kube_pod_container_status_running` | コンテナが実行中かどうか | `0/1` | 主要コンテナが存在しない `1` サービスが利用できないことを示します |

調査の推奨事項: コンテナの異常については、まずステータスの理由を確認し、その後再起動回数と最新の再起動時間を確認してください。再起動が頻繁な場合は、アプリケーションログ、 OOM イベント、およびプローブ設定を使用して調査を続けます。 

## 3. Kubernetes クラスタ監視

Kubernetes 監視は、クラスタのリソース使用状況、コントロールプレーンの健全性、ワークロードレプリカの状態、およびストレージオブジェクトの状態を評価するために使用されます。主な指標は、kube-state-metrics、kubelet、およびAPIServerから取得されます。 

### 3.1 ノードの容量とスケジュール可能なリソース

| 監視次元 | メトリック | メトリクスの意味 | 共通スコープ/単位 | 異常な現れ |
| --- | --- | --- | --- | --- |
| ノード容量 | `kube_node_status_capacity` | ノードの総容量 | CPU、メモリ、Podの数など | 容量計画に使用 |
| 割り当て可能なリソース | `kube_node_status_allocatable` | ノードのスケジュール可能なリソース | CPU、メモリ、Podの数など | スケジュール可能なリソースが不足すると、Podは保留状態になります |
| ノードの状態 | `kube_node_status_condition` | ノードReady、MemoryPressure、その他の状態 | `0/1` | Ready状態の異常やPressureの発生は即時対応が必要です |
| スケジューリング禁止 | `kube_node_spec_unschedulable` | ノードがコーディングされているか | `0/1` | '1'に設定されている場合、ノードは新しいPodをスケジュールしません |
| ノード情報 | `kube_node_info` | ノードのバージョン、カーネル、コンテナランタイム情報 | タグ情報 | バージョンの違いをトラブルシューティングするために使用 |

トラブルシューティングの提案: Podが保留中の場合、まず割り当て可能リソースとリクエストを確認し、次にノードが'schedulableでない'かを確認し、最後にノードの状態でリソース圧力が発生しているかを確認します。 

### 3.2 Podのステータス 

| 監視の指標 | 指標 | インジケーターの意味 | 一般的なアパーチャ/単位 | 異常な動作 |
| --- | --- | --- | --- | --- |
| ポッド情報 | `kube_pod_info` | ポッドのネームスペース、ノードなどの情報 | タグ情報 | ポッド分布の特定に使用 |
| ポッドのステージ | `kube_pod_status_phase` | 保留中、実行中、成功、失敗の状態など | `0/1` | 保留中/失敗の増加はスケジューリングや実行の異常を示す |
| ポッド準備完了 | `kube_pod_status_ready` | ポッドは準備完了ですか | `0/1` | 準備未完了はサービスの可用性に影響する |
| ポッド理由 | `kube_pod_status_reason` | ポッド異常理由 | ラベル値 | 退避、ノード喪失などは調査が必要 |
| コンテナ再起動 | `kube_pod_container_status_restarts_total` | コンテナ再起動の回数 | 回/増分 | 再起動の増加は安定性の問題を示す |
| コンテナ待機中 | `kube_pod_container_status_waiting` | コンテナが待機状態かどうか | `0/1` | 待機状態が続く場合、Podは正常にサービスを提供できません |
| 待機理由 | `kube_pod_container_status_waiting_reason` | 待機状態の理由 | ラベル値 | イメージプル失敗、CrashLoopなど |
| コンテナが終了しました | `kube_pod_container_status_terminated` | コンテナが終了しているかどうか | `0/1` | 予期しない終了は、再起動やログと一緒に確認する必要があります |

よくある質問:

```promql
sum by (namespace, phase) (kube_pod_status_phase == 1)
```

```promql
increase(kube_pod_container_status_restarts_total[10m])
```

調査の提案: Podに異常がある場合、Podフェーズだけでなく、Readyステータス、理由、コンテナの待機理由を確認すると、具体的な問題をよりよく示せます

### 3.3 リソース要求と制限

| 監視次元 | メトリック | メトリクスの意味 | 一般的な測定/単位 | 異常な動作 |
| --- | --- | --- | --- | --- |
| 要求されたリソース | `kube_pod_container_resource_requests` | コンテナのリソース要求 | CPU, メモリ | 要求が高すぎるとスケジューリングに影響し、低すぎると安定性に影響します |
| リソース制限 | `kube_pod_container_resource_limits` | コンテナの制限 | CPU, メモリ | 制限が低すぎると、 CPU スロットリングや OOM |
| ノード割り当て可能 | `kube_node_status_allocatable` | ノードでスケジューリング可能なリソース | CPU, メモリ | クラスタリソース割り当て率の計算に使用 |
| コンテナ使用量 | `container_cpu_usage_seconds_total`, `container_memory_working_set_bytes` | 実際の CPU CPUおよびメモリ使用量 | コア/秒、バイト | リクエスト/制限が妥当かどうかを判断するために使用 |

よくあるクエリ:

```promql
sum(kube_pod_container_resource_requests{resource="cpu"}) / sum(kube_node_status_allocatable{resource="cpu"}) * 100
```

```promql
sum(kube_pod_container_resource_requests{resource="memory"}) / sum(kube_node_status_allocatable{resource="memory"}) * 100
```

調査の提案: リソース計画は「リクエストされた値」と「実際の使用値」の両方を考慮すべきです。リクエストだけを見ると業務負荷を誤判断する可能性があり、使用量だけを見るとスケジューリング容量を見落とす可能性があります。

### 3.4 ワークロードレプリカ

| 監視次元 | メトリック | メトリクスの意味 | 共通スコープ/単位 | 異常なパフォーマンス |
| --- | --- | --- | --- | --- |
| デプロイメントレプリカ | `kube_deployment_status_replicas` | 現在のデプロイメントレプリカ数 | 単位 | 期待されるレプリカと一致しない |
| 更新されたレプリカ | `kube_deployment_status_replicas_updated` | 新しいバージョンに更新されたレプリカの数 | 単位 | リリース中に長時間成長がない |
| 利用できないレプリカ | `kube_deployment_status_replicas_unavailable` | 利用できないレプリカの数 | 単位 | 0より大きい場合、サービス容量が減少する |
| StatefulSet レプリカ | `kube_statefulset_status_replicas` | 現在の StatefulSet レプリカ数 | 単位 | ステートフルサービスの異常レプリカ |
| StatefulSet 準備完了 | `kube_statefulset_status_replicas_ready` | 準備完了の StatefulSet レプリカ数 | 単位 | Ready が期待されるレプリカより少ない場合、サービスは不完全である |

調査の推奨事項: リリース異常時は確認する `updated` および `unavailable`. StatefulSet の異常については、注意する PVC、Pod の起動順序、およびノード親和性。

### 3.5 ジョブとバッチタスク

| 監視次元 | メトリック | メトリクスの意味 | 一般的な標準/単位 | 異常なパフォーマンス |
| --- | --- | --- | --- | --- |
| 実行中のジョブ | `kube_job_status_active` | 現在アクティブなジョブの数 | カウント | 長期間アクティブなジョブは、停止したジョブである可能性があります |
| 失敗したジョブ | `kube_job_status_failed` | ジョブの失敗回数 | カウント | 失敗の増加はジョブログの確認が必要です |
| 成功したジョブ | `kube_job_status_succeeded` | 正常に完了したジョブの数 | カウント | タスク完了の判断に使用されます |
| 完了時間 | `kube_job_status_completion_time` | ジョブ完了時間 | Unixタイムスタンプ | 完了時間がない場合は、ジョブが完了していないことを示している可能性があります |

調査の推奨事項: バッチタスクに異常がある場合は、 `active`, `failed`、および `succeeded` 失敗だけを見ると、長時間停止しているタスクを見逃す可能性があるため、一緒に確認してください。

### 3.6 PVC およびストレージオブジェクト

| 監視次元 | メトリック | メトリクスの意味 | 一般的な標準/単位 | 異常なパフォーマンス |
| --- | --- | --- | --- | --- |
| PVC ステータス | `kube_persistentvolumeclaim_status_phase` | PVC バウンド、保留、およびその他のステータス | `0/1` | 保留中の場合、Podはストレージをマウントできません |
| PVC 要求容量 | `kube_persistentvolumeclaim_resource_requests_storage_bytes` | 要求されたストレージ容量 PVC | バイト | 容量計画およびクォータ管理に使用されます |

トラブルシューティングの提案: ステートフルサービスが起動に失敗した場合、Podを確認するだけでなく、 PVC がバインドされているか、ストレージクラスが利用可能か、基盤となるストレージの容量が不足していないかを確認する必要があります。

### 3.7 APIServer、etcd、およびコントロールプレーン

| 監視次元 | メトリック | メトリクスの意味 | 共通の指標/単位 | 異常なパフォーマンス |
| --- | --- | --- | --- | --- |
| APIServer リクエスト数 | `apiserver_request_total` | APIServer リクエストの累積数 | リクエスト/秒 | 突然のリクエストの急増は、コントローラー、 kubectlまたは業務コンポーネントから発生する場合があります |
| APIServer レイテンシ | `apiserver_request_duration_seconds_bucket` | APIServer リクエストの所要時間のバケット | P50/P95/P99 | レイテンシの増加は、スケジューリング、デプロイメント、およびコントローラーの同期に影響を与えます |
| etcd レイテンシ | `etcd_request_duration_seconds_bucket` | etcd リクエストの期間バケット | P50/P95/P99 | etcd が遅いと、コントロールプレーン全体が遅くなる可能性があります |
| キュー待機 | `workqueue_queue_duration_seconds_bucket` | コントローラーのキュー待機時間 | パーセンタイル期間 | キューのバックログ、リソース状態の同期が遅くなる |
| キュー処理 | `workqueue_work_duration_seconds_bucket` | コントローラー処理時間 | パーセンタイル期間 | コントローラーの処理が遅くなる |

よくある質問:

```promql
sum by (verb, resource) (rate(apiserver_request_total[5m]))
```

```promql
histogram_quantile(0.95, sum(rate(apiserver_request_duration_seconds_bucket[5m])) by (le, verb, resource))
```

調査の推奨事項: コントロールプレーンの問題は通常、デプロイの遅延、Pod 状態の更新が遅い、応答が遅い、という形で現れます kubectl APIServer の待機時間と etcd の待機時間が同時に増加する場合、まず etcd、ディスク IO、およびコントロールプレーンノードの負荷を確認してください。

## 4. MySQL 監視

MySQL 監視は、インスタンスの可用性、接続の負荷を観察するために使用されます SQL リクエスト量、遅いクエリ、キャッシュヒット、テンポラリテーブル、ロック待機、ファイルハンドル、およびネットワークスループット。

### 4.1 インスタンスの状態とリクエスト量

| 監視次元 | メトリック | メトリクスの意味 | 共通スコープ/単位 | 異常な現れ |
| --- | --- | --- | --- | --- |
| インスタンス稼働状況 | `up` | MySQLエクスポーターが収集可能かどうか | `0/1` | ~の場合 `0`インスタンス、ネットワーク、またはエクスポーターが異常であるか |
| 稼働時間 | `mysql_global_status_uptime` | MySQL 稼働時間 | 秒 | 減少はインスタンスの再起動を示す |
| 総クエリ数 | `mysql_global_status_queries` | 累積クエリ数 | 回/秒 | QPS 急増はビジネスのピークまたは異常なリクエストを示す場合がある |
| クエリ | `mysql_global_status_questions` | クライアントが開始したステートメントの累積数 | 回/秒 | リクエストの負荷を評価するためにクエリと併せて確認する |
| コマンド統計 | `mysql_global_status_commands_total` | 各種コマンドの累積数 | 回/秒 | select、insert、update、deleteなどのコマンドを区別できる |

よくあるクエリ: 

```promql
rate(mysql_global_status_queries[5m])
```

```promql
sum by (command) (rate(mysql_global_status_commands_total[5m]))
```

調査の提案: 〜の場合 QPS 上昇したら、まずコマンドの分布を確認する。もし `select` スキャンタイプの指標とともに増加する場合は、インデックスや遅いクエリに注意する。書き込みコマンドが増加する場合は、ロック待ち、ディスクIO、ホスト書き込み遅延の監視を続ける。

### 4.2 接続とスレッド

| 監視次元 | メトリック | メトリクスの意味 | 共通の指標/単位 | 異常なパフォーマンス |
| --- | --- | --- | --- | --- |
| 現在の接続数 | `mysql_global_status_threads_connected` | 現在接続中のスレッド数 | 数 | 上限に近づくと、新しい接続が失敗する可能性がある |
| アクティブスレッド | `mysql_global_status_threads_running` | 現在実行中のスレッド数 | 数 | 継続的な増加は通常、遅い実行またはロック待ちを示す SQL 実行中またはロック待ち |
| 過去の最大接続数 | `mysql_global_status_max_used_connections` | 過去に使用された最大接続数 | 数 | 最大接続に近づいています_接続プールの評価が必要であることを示しています |
| 最大接続数 | `mysql_global_variables_max_connections` | MySQL 最大接続の設定 | 数 | 接続使用率を計算するために使用されます |
| 異常クライアント | `mysql_global_status_aborted_clients` | クライアントの異常切断の累計数 | 回/秒 | ネットワークの問題、タイムアウト、またはクライアント側の例外 |
| 接続失敗 | `mysql_global_status_aborted_connects` | 接続失敗の合計数 | 回/秒 | 認証エラー、接続制限、ネットワーク異常など |

よくあるクエリ:

```promql
mysql_global_status_threads_connected / mysql_global_variables_max_connections * 100
```

調査の提案: 接続数が多いことは必ずしもデータベースが遅いことを意味するわけではなく、アプリケーション接続プールの設定不備による場合もあります。 `Threads_running` 長時間高い状態が続くことはより懸念されます。通常、これは SQL 実行またはロック待ちの問題。

### 4.3 遅いクエリ、スキャン、およびソート

| 監視次元 | メトリック | メトリクスの意味 | 一般的な測定/単位 | 異常な動作 |
| --- | --- | --- | --- | --- |
| 遅いクエリ | `mysql_global_status_slow_queries` | 遅いクエリの累積数 | 回/秒 | 増加はより多くの遅延を示す SQL |
| フルジョインスキャン | `mysql_global_status_select_full_join` | インデックスなしのジョインの数 | 回/秒 | ジョイン条件のインデックスが欠けている可能性を示す |
| フルテーブルスキャン | `mysql_global_status_select_scan` | フルテーブルスキャンの数 | 回/秒 | 大規模テーブルでのフルスキャンはインスタンスを遅くする可能性がある |
| ソートマージ | `mysql_global_status_sort_merge_passes` | ソートに複数回のマージが必要な回数 | 回/秒 | ソートバッファが不足しているか、ソートするデータが多すぎる |

調査の提案: 遅いクエリが増加した場合、業務リリース時間と照らして確認する SQL 変更記録。スキャンとソートの指標が上昇した場合、通常はスローログ、実行計画、およびインデックス設計を参照します。

### 4.4 InnoDB バッファプール

| 監視次元 | メトリック | メトリクスの意味 | 共通の指標/単位 | 異常なパフォーマンス |
| --- | --- | --- | --- | --- |
| バッファプールサイズ | `mysql_global_variables_innodb_buffer_pool_size` | InnoDB バッファプール構成サイズ | バイト | 小さすぎるとディスク読み取りが増加します |
| バッファプールページ | `mysql_global_status_buffer_pool_pages` | さまざまな種類のバッファプールページの数 | ページ | 汚れたページ、空きページ、データページ、その他のページを監視するために使用されます |
| ページサイズ | `mysql_global_status_innodb_page_size` | InnoDBページサイズ | バイト | ページ数を容量に変換するために使用されます |

調査の提案: バッファプールのヒット率が低い場合、データベースはディスクにより多くアクセスします。ノードのディスク読み取りスループット、読み取り、およびiowaitと併せて評価する必要があります。 IOPS

### 4.5 一時テーブル、テーブルキャッシュ、およびファイルハンドル

| 監視次元 | メトリック | メトリクスの意味 | 共通の指標/単位 | 異常なパフォーマンス |
| --- | --- | --- | --- | --- |
| 一時テーブル | `mysql_global_status_created_tmp_tables` | 作成された一時テーブルの総数 | 回/秒 | クエリの複雑さの増加 |
| ディスク一時テーブル | `mysql_global_status_created_tmp_disk_tables` | 作成されたディスク一時テーブルの総数 | 回/秒 | ディスクI/Oの負荷増大、 SQL 処理が遅くなる可能性がある |
| 一時ファイル | `mysql_global_status_created_tmp_files` | 作成された一時ファイルの総数 | 回/秒 | 一時ファイルの増加 |
| テーブルロック即時 | `mysql_global_status_table_locks_immediate` | テーブルロックが即座に取得された回数 | 回/秒 | 通常の参照指標 |
| テーブルロック待機 | `mysql_global_status_table_locks_waited` | テーブルロックを待機した回数 | 回/秒 | ロック競合の増加 |
| テーブルキャッシュヒット | `mysql_global_status_table_open_cache_hits` | テーブルオープンキャッシュヒットの回数 | 回/秒 | ヒットが少ない場合、テーブル開放が頻繁であることを示す可能性がある |
| テーブルキャッシュミス | `mysql_global_status_table_open_cache_misses` | テーブルオープンキャッシュミスの数 | 回/秒 | テーブルキャッシュの評価が必要 |
| テーブルキャッシュオーバーフロー | `mysql_global_status_table_open_cache_overflows` | テーブルオープンキャッシュオーバーフローの数 | 回/秒 | 設定不足またはテーブルが多すぎる |
| オープンテーブル | `mysql_global_status_open_tables` | 現在のオープンテーブル数 | 個 | キャッシュ制限に近づくとリスクが増加 |
| テーブルキャッシュ設定 | `mysql_global_variables_table_open_cache` | 表_開く_設定されたキャッシュ値 | 個 | 使用率計算用 |
| オープンファイル | `mysql_global_status_open_files` | 現在のオープンファイル数 | 個 | 影響を与える可能性がある SQL ファイル制限に近づくと実行に影響 |
| ファイル制限 | `mysql_global_variables_open_files_limit` | MySQL ファイルハンドルの制限 | 個 | ファイルハンドル使用率を計算するために使用 |

トラブルシューティングの提案: 一時テーブル、ロック待ち、テーブルキャッシュのミスは、遅いクエリと一緒に発生することがよくあります。ディスク一時テーブルが増加した場合は、ノードの書き込みI/O、ディスクのレイテンシ、および SQL ソート/グループ化に注意してください。

### 4.6 ネットワークスループット

| 監視次元 | メトリック | メトリクスの意味 | 共通単位 | 異常なパフォーマンス |
| --- | --- | --- | --- | --- |
| 着信トラフィック | `mysql_global_status_bytes_received` | 累積 MySQL 受信バイト数 | バイト/秒 | リクエストボディや書き込みトラフィックの増加 |
| 発信トラフィック | `mysql_global_status_bytes_sent` | 累積送信バイト数 MySQL | バイト/秒 | 大きなクエリ、フルテーブルスキャン、バルクエクスポートはアウトバウンドトラフィックを増加させます |

よくある質問:

```promql
rate(mysql_global_status_bytes_received[5m])
```

```promql
rate(mysql_global_status_bytes_sent[5m])
```

調査の提案: 〜の場合 MySQL アウトバウンドトラフィックが突然増加した場合、通常は大きな結果セット、エクスポートタスク、ページネーションのないクエリに注意する必要があります。

## 5. MongoDB 監視

MongoDB 監視は、インスタンスの状態、接続数、操作量、クエリスキャン、メモリ使用量、ネットワークスループット、およびレプリケーションバッファの状況を観察するために使用されます。

### 5.1 インスタンスと接続

| 監視次元 | メトリック | メトリクスの意味 | 一般的な測定/単位 | 異常なパフォーマンス |
| --- | --- | --- | --- | --- |
| インスタンス稼働状況 | `up` | mongo exporter がデータを収集できるかどうか | `0/1` | もし `0`、インスタンスまたはエクスポーターに異常があります |
| 稼働時間 | `mongodb_ss_uptime` | MongoDB 稼働時間 | 秒 | 値が小さいほど、インスタンスの再起動を示します |
| 接続数 | `mongodb_ss_connections` | 現在の接続関連統計 | 数 | 異常に高い接続数は、接続プールまたはビジネスピークを示している可能性があります |

調査の提案：接続数が上昇した場合、まずビジネスピーク、接続プール構成の変更、またはクライアントの異常な再接続がないか確認してください。

### 5.2 操作とドキュメント処理

| 監視次元 | メトリック | メトリクスの意味 | 一般的な測定/単位 | 異常なパフォーマンス |
| --- | --- | --- | --- | --- |
| 操作数 | `mongodb_ss_opcounters` | 挿入、クエリ、更新、削除などの操作の累積回数 | 回/秒 | 特定の種類の操作が急増することは、ビジネスアクセスパターンの変化を示す |
| ドキュメント処理 | `mongodb_ss_metrics_document` | 挿入、更新、削除、返却などのドキュメントの累積数 | 回/秒 | 返却が実際に必要な数よりも大幅に多い場合、結果セットが大きすぎる可能性がある |
| インデックスエントリのスキャン | `mongodb_ss_metrics_queryExecutor_scanned` | クエリ中にスキャンされたインデックスエントリの数 | 回/秒 | 過剰なスキャンは、インデックスの不適切さを示している可能性がある |
| スキャンされたドキュメント | `mongodb_ss_metrics_queryExecutor_scannedObjects` | クエリ中にスキャンされたドキュメントの数 | 回/秒 | ドキュメントのスキャンが多いことは、クエリ効率の低さを示す |

よくある問い合わせ: 

```promql
sum by (type) (rate(mongodb_ss_opcounters[5m]))
```

調査の推奨事項: よく見られる現れ方 MongoDB 遅いクエリは、scanned/scannedObjectsの増加です。遅いログやインデックスヒットと組み合わせて分析する必要があります。

### 5.3 メモリ、ネットワーク、ディスク

| 監視次元 | メトリック | メトリクスの意味 | 共通の単位/測定 | 異常な現れ |
| --- | --- | --- | --- | --- |
| 常駐メモリ | `mongodb_ss_mem_resident` | MongoDB 常駐メモリ | MB または バイト | 継続的な増加はホストメモリの確認が必要です |
| 仮想メモリ | `mongodb_ss_mem_virtual` | MongoDB 仮想メモリ | MB または バイト | 単独の増加は必ずしも実際の負荷を示すわけではありません |
| 受信トラフィック | `mongodb_ss_network_bytesIn` | MongoDB 累積受信バイト数 | バイト/秒 | 書き込みまたは要求トラフィックの増加 |
| 送信トラフィック | `mongodb_ss_network_bytesOut` | MongoDB 累積送信バイト数 | バイト/秒 | 大きなクエリやエクスポートタスクによる送信トラフィックの増加 |
| ホスト読み取り IO | `node_disk_reads_completed_total` | 読み取り IOPS が存在するノードで MongoDB 所在する | 回/秒 | クエリスキャンによる読み取り IO の増加 |
| ホスト書き込み IO | `node_disk_writes_completed_total` | 書き込み IOPS が存在するノードで MongoDB が配置されている | 回/秒 | 書き込みまたはジャーナルの圧力の増加 | 

トラブルシューティングの提案: MongoDB メモリおよびディスクのパフォーマンスは、ノードのメモリおよびディスクIOと合わせて考慮する必要があります。インスタンスのメトリクスをホストのディスク読み書きと一緒に確認すると、 MongoDB 問題がシステム自体によるものか、それとも基盤のリソースが遅いのかを判断しやすくなります。 

### 5.4 レプリケーションバッファ 

| 監視次元 | メトリック | メトリクスの意味 | 一般的な測定/単位 | 異常なパフォーマンス | 
| --- | --- | --- | --- | 
| レプリケーションバッファサイズ | `mongodb_ss_metrics_repl_buffer_sizeBytes` | レプリケーションバッファのサイズ | バイト | バッファが継続的に増加している場合、レプリケーションの消費がタイムリーに行われていないことを示します | 

トラブルシューティングの提案: 異常なレプリケーションバッファは通常、スレーブの処理能力、ネットワーク、またはディスク書き込みに関連しています。レプリケーション遅延、ノードのネットワーク、およびディスク書き込みのメトリクスと一緒に分析する必要があります。 

## 6. Redis 監視 

Redis 監視は、インスタンスの可用性、接続数、コマンド処理、メモリレベル、キー空間、ヒット率、強制削除、およびネットワークスループットを観察するために使用されます。 

### 6.1 インスタンスとクライアント 

| 監視次元 | メトリック | メトリクスの意味 | 一般的な測定/単位 | 異常なパフォーマンス | 
| --- | --- | --- | --- | --- |
| インスタンス稼働状況 | `up` | 実行が成功したかどうか; 失敗した場合は、エラープロンプトと連携してトラブルシューティングを行います。 Redis エクスポーターを収集できます | `0/1` | ~の場合 `0`、インスタンスまたはエクスポーターに異常があります |
| 稼働時間 | `redis_uptime_in_seconds` | Redis 稼働時間 | 秒 | 減少はインスタンスの再起動を示します |
| クライアント接続 | `redis_connected_clients` | 現在のクライアント接続数 | 数 | 急激な増加は接続プールや再接続ストームを示す可能性があります |

### 6.2 コマンド、メモリ、キー空間

| 監視次元 | メトリック | メトリクスの意味 | 共通単位 | 異常な動作 |
| --- | --- | --- | --- | --- |
| 処理されたコマンド | `redis_commands_processed_total` | 総数 Redis 処理されたコマンド | 回/秒 | 突然の QPS 急増はインスタンスに影響を与える可能性があります CPU |
| コマンド分類 | `redis_commands_total` | タイプ別のコマンド総数 | 回/秒 | get、set、delなどのコマンドの変化を特定できます |
| 使用中のメモリ | `redis_memory_used_bytes` | 現在 Redis メモリ使用量 | バイト | 最大メモリに近づくと、追い出しが発生する可能性があります |
| 最大メモリ | `redis_memory_max_bytes` | Redis maxmemory の設定 | バイト | メモリ使用率を計算するために使用 |
| キーの数 | `redis_db_keys` | 各DBのキーの数 | 数 | キーの異常な増加は、期限切れなしのキャッシュや書き込み異常を示している可能性があります |
| 有効期限付きキー | `redis_db_keys_expiring` | 有効期限が設定されているキーの数 | 数 | 低い割合はキャッシュのライフサイクルに注意が必要です |

よくあるクエリ:

```promql
rate(redis_commands_processed_total[5m])
```

```promql
redis_memory_used_bytes / redis_memory_max_bytes * 100
```

### 6.3 ヒット率、追い出し、ネットワーク

| 監視次元 | メトリック | メトリクスの意味 | 共通の指標/単位 | 異常なパフォーマンス |
| --- | --- | --- | --- | --- |
| ヒット数 | `redis_keyspace_hits_total` | キーのヒット総数 | 回/秒 | ミスとともにヒット率を計算 |
| ミス数 | `redis_keyspace_misses_total` | キーのミス総数 | 回/秒 | ミスの増加はバック・トゥ・ソースの負荷増加につながる可能性があります |
| 期限切れキー | `redis_expired_keys_total` | 期限切れキーの総数 | 回/秒 | 有効期限の集中消去（Expiry storms）は CPU ジッターを引き起こす可能性があります |
| 追い出されたキー | `redis_evicted_keys_total` | 削除されたキーの総数 | 回/秒 | 成長はメモリの圧迫または maxmemory 不足を示します |
| 着信トラフィック | `redis_net_input_bytes_total` | 受信した総バイト数 Redis | バイト/秒 | 書き込みまたは要求トラフィックの増加 |
| 発信トラフィック | `redis_net_output_bytes_total` | 送信した総バイト数 Redis | バイト/秒 | 大量の値またはバッチ読み取りによって引き起こされる高い送信トラフィック |

よくあるクエリ:

```promql
rate(redis_keyspace_hits_total[5m]) / (rate(redis_keyspace_hits_total[5m]) + rate(redis_keyspace_misses_total[5m])) * 100
```

```promql
rate(redis_evicted_keys_total[5m])
```

調査の推奨: Redisでは、メモリと削除のリスクに焦点を当てます。ヒット率の低下はバックエンドデータベースへの圧力を転送します。削除の増加は、キャッシュ容量または削除戦略の評価が必要であることを示します。

## 7. Kafka 監視

Kafka 監視は、ブローカー数、トピック/パーティションの状態、プロデュースおよびコンシュームのオフセット、コンシューマーグループの遅延、メンバー数、およびレプリカ同期の状態を観察するために使用されます。

### 7.1 ブローカー、トピックおよびパーティション

| 監視次元 | メトリック | メトリクスの意味 | 共通の指標/単位 | 異常なパフォーマンス |
| --- | --- | --- | --- | --- |
| ブローカーの数 | `kafka_brokers` | 現在表示されているブローカーの数 | 個 | 数が減少している場合、ブローカーが利用できないか、エクスポーターにアクセスできないことを示します |
| トピックパーティション | `kafka_topic_partitions` | トピックのパーティション数 | 個 | パーティションの変更は、同時実行性や消費能力に影響します |
| 現在のパーティションオフセット | `kafka_topic_partition_current_offset` | パーティションの最新オフセット | オフセット / 増加率 | 継続的なプロダクション書き込み中は連続的に増加する必要があります |
| パーティション最古オフセット | `kafka_topic_partition_oldest_offset` | パーティション最古オフセット | オフセット | 保持データの範囲を観察するために使用されます |

よくあるクエリ: 

```promql
sum by (topic) (rate(kafka_topic_partition_current_offset[5m]))
```

調査提案: 生産率が異常な場合、まずトピックの現在のオフセット成長を確認してください。ビジネスが書き込みを確認しており、オフセットが増加していない場合は、プロデューサー側のエラー、ブローカーの状態、およびトピック設定を確認してください。

### 7.2 コンシューマーグループと遅延

| 監視次元 | メトリック | メトリクスの意味 | 一般的な測定/単位 | 異常なパフォーマンス |
| --- | --- | --- | --- | --- |
| 消費オフセット | `kafka_consumergroup_current_offset` | コンシューマーグループによって消費された現在のオフセット | オフセット / 増加率 | 成長がない場合、消費が停止しているか、スタックしていることを示します |
| パーティション遅延 | `kafka_consumergroup_lag` | パーティション上のコンシューマーグループのバックログ | 数 | 遅延が増加している場合、消費が生産に追いついていないことを示します |
| グループ合計遅延 | `kafka_consumergroup_lag_sum` | コンシューマーグループの合計バックログ | 数 | 合計遅延が継続的に増加している場合、ビジネスの遅延が拡大していることを示します |
| グループメンバー | `kafka_consumergroup_members` | コンシューマーグループのメンバー数 | 数 | メンバー数の減少は消費能力の低下につながる可能性があります |

よくあるクエリ:

```promql
sum by (consumergroup, topic) (kafka_consumergroup_lag)
```

```promql
sum by (consumergroup, topic) (rate(kafka_consumergroup_current_offset[5m]))
```

トラブルシューティングの提案: コアビジネスメトリクスは Kafka Lag です。Lag が増加した場合、最初にコンシューマーメンバーの数が減少していないかを確認し、次に消費速度が低下していないかを確認し、最後にアプリケーションの処理時間、下流の依存関係、Broker の IO を確認します。

### 7.3 レプリカと ISR

| 監視次元 | メトリック | メトリクスの意味 | 一般的な測定/単位 | 異常なパフォーマンス |
| --- | --- | --- | --- | --- |
| レプリカの数 | `kafka_topic_partition_replicas` | パーティションレプリカの数 | 数 | 予想より少ないレプリカは信頼性を低下させます |
| ISR レプリカ | `kafka_topic_partition_in_sync_replica` | パーティションインシンクレプリカの数 | 数 | の低下は ISR レプリカの遅延や Broker の問題を示します |
| 優先リーダー | `kafka_topic_partition_leader_is_preferred` | リーダーが優先レプリカかどうか | `0/1` | 長期的な不均衡は、一部のブローカーに高い負荷をかける可能性があります |

トラブルシューティングの提案: 低下は ISR 通常の遅延よりも信頼性リスクを示します。ブローカーの状態、ネットワーク、ディスク書き込み遅延、レプリカ同期を確認してください。

## 8. MinIO オブジェクトストレージの監視

MinIO 監視は、オブジェクトストレージクラスタ、ノードおよびディスクの状態、バケット容量、 S3 リクエスト、エラー、トラフィック、プロセスハンドル、修復タスクの活動を観察するために使用されます。 

### 8.1 クラスタノードとディスク 

| 監視次元 | メトリック | メトリクスの意味 | 共通の指標/単位 | 異常な現れ |
| --- | --- | --- | --- | --- |
| オンラインノード | `minio_cluster_nodes_online_total` | オンラインノード数 MinIO ノード | 個 | 数の減少はノードが利用不可であることを示します |
| オフラインノード | `minio_cluster_nodes_offline_total` | オフラインノード数 MinIO ノード | 個 | 0より大きい場合は、クラスタの可用性に注意が必要です |
| オンラインディスク | `minio_cluster_disk_online_total` | オンラインディスクの数 | 個 | ディスクの減少は冗長性と書き込み能力に影響します |
| オフラインディスク | `minio_cluster_disk_offline_total` | オフラインディスクの数 | 個 | 0を超える場合はディスクまたはマウントのトラブルシューティングが必要です |
| 使用可能容量 | `minio_cluster_capacity_usable_free_bytes` | クラスタ使用可能容量 | バイト | 継続的な減少は容量不足のリスクを示します |

トラブルシューティングの提案: オブジェクトストレージの場合、まずノードとディスクのオンライン状態を確認してください。オフラインディスクは数量だけで評価せず、消去符号の冗長戦略と組み合わせてリスクを判断する必要があります。 

### 8.2 バケット容量とオブジェクト数

| 監視次元 | メトリック | メトリクスの意味 | 共通の指標/単位 | 異常なパフォーマンス |
| --- | --- | --- | --- | --- |
| バケット容量 | `bucket_usage_size` | バケットの使用容量 | バイト | 急速な容量の増加には拡張の評価が必要です |
| オブジェクト数 | `bucket_objects_count` | バケット内のオブジェクト数 | カウント | 小さなオブジェクトが多すぎると、メタデータやスキャンの負荷が増加します |
| オブジェクトサイズの分布 | `minio_bucket_objects_size_distribution` | バケット内のオブジェクトサイズの分布 | バケット別統計 | オブジェクト分布の変化は、ストレージおよびリクエストのパフォーマンスに影響します |

よくあるクエリ:

```promql
sum by (bucket) (bucket_usage_size)
```

```promql
sum by (bucket) (bucket_objects_count)
```

調査推奨事項: 容量の増加はバケットごとに個別に分析する必要があります。オブジェクト数が急速に増加しても容量の増加が明らかでない場合、通常は小さなオブジェクトの増加によるものです。ライフサイクルのクリーンアップやビジネス書き込みパターンに注意する必要があります。

### 8.3 S3 リクエスト、エラー、トラフィック

| 監視次元 | メトリック | メトリクスの意味 | 一般的な測定/単位 | 異常なパフォーマンス |
| --- | --- | --- | --- | --- |
| S3 リクエスト数 | `minio_s3_requests_total` | 累積数 S3 API リクエスト | 回/秒 | リクエストの急増は、ビジネスのピークや再試行が原因の可能性があります |
| S3 エラー件数 | `minio_s3_requests_errors_total` | 累積数 S3 API エラー | 回/秒 | オブジェクトの読み書きに影響するエラー率の上昇 |
| 受信トラフィック | `minio_s3_traffic_received_bytes` | 累積 S3 受信バイト数 | バイト/秒 | アップロードトラフィックの増加 |
| 送信トラフィック | `minio_s3_traffic_sent_bytes` | 累積 S3 送信バイト数 | バイト/秒 | ダウンロードまたはオリジン取得トラフィックの増加 |

よくあるクエリ:

```promql
sum by (api) (rate(minio_s3_requests_total[5m]))
```

```promql
sum(rate(minio_s3_requests_errors_total[5m])) / sum(rate(minio_s3_requests_total[5m])) * 100
```

調査の推奨: エラー率が増加した場合、まずはそれを分類する S3 エラー率が増加した場合、まずそれをカテゴリーごとに分解する API タイプを入力し、対応するバケット、ノードディスクの状態、およびネットワークトラフィックを確認します。

### 8.4 ノードプロセス、ファイルハンドル、および IO

| 監視次元 | メトリック | メトリクスの意味 | 共通の指標/単位 | 異常な現れ |
| --- | --- | --- | --- | --- |
| ノードディスク使用量 | `minio_node_disk_used_bytes` | のディスク使用量 MinIO ノード | バイト | 単一ノードの容量不均衡 |
| オープンファイルハンドル | `minio_node_file_descriptor_open_total` | によって開かれたファイルハンドルの数 MinIO プロセス | カウント | システム制限に近づくとリクエストが失敗することがあります |
| システムコールの読み取り | `minio_node_syscall_read_total` | 読み込みシステムコールの累積数 | 回/秒 | 読み込みコールの異常な増加 |
| 書き込みシステムコール | `minio_node_syscall_write_total` | 書き込みシステムコールの累積数 | 回/秒 | 書き込みコールの異常な増加 |
| プロセス読み取りバイト数 | `minio_node_io_rchar_bytes` | プロセスによって読み取られた累積バイト数 | バイト/秒 | 読み取り負荷の増加 |
| プロセス書き込みバイト数 | `minio_node_io_wchar_bytes` | プロセスによって書き込まれた累積バイト数 | バイト/秒 | 書き込み負荷の増加 |
| ゴルーチンの数 | `minio_node_go_routine_total` | ゴルーチンの数（中） MinIO プロセス | カウント | 継続的な増加はリクエストバックログやリークを示す可能性があります |
| 開始時間 | `minio_node_process_starttime_seconds` | MinIO プロセス開始時間 | Unixタイムスタンプ | 変化はプロセス再起動を示します |

調査の提案: MinIO パフォーマンス問題の場合、考慮してください S3 リクエスト、ノードディスク、プロセスIO、およびゴルーチンをまとめて。リクエスト量が多いだけでは必ずしも異常ではありません。エラー率、IO遅延、およびディスクのオフライン状態がより明確なリスクの指標です。

### 8.5 ヒールと使用状況アクティビティ

| 監視次元 | メトリック | メトリクスの意味 | 一般的な標準/単位 | 異常な動作 |
| --- | --- | --- | --- | --- |
| ヒールアクティビティ | `minio_heal_time_last_activity_nano_seconds` | 最後のヒールアクティビティ時間 | ナノ秒タイムスタンプ | 長時間または頻繁なヒールはディスクの健康状態に注意を要します |
| 使用状況アクティビティ | `minio_usage_last_activity_nano_seconds` | 最後の使用状況スキャンアクティビティ時間 | ナノ秒タイムスタンプ | 異常な使用状況スキャンは容量統計の精度に影響を与える可能性があります |

調査の提案: ノードまたはディスクの異常回復後、ヒールアクティビティが正常に進行しているかを監視し、オブジェクト冗長性が長時間リスクにさらされないようにしてください。

## 9. Elasticsearch 監視

Elasticsearch 監視は、検索クラスターの健全性、ノード数、シャードの分布、インデックスの読み書き操作、キャッシュ、 JVMスレッドプール、ディスク、およびネットワークの監視に使用されます。ESの障害は通常、単一の指標によって決まるものではなく、より一般的には「シャードの異常   JVM プレッシャー　スレッドプールの拒否　ディスクのウォーターマーク」が同時に現れます。

### 9.1 クラスターの健全性とノード

| 監視次元 | メトリック | メトリクスの意味 | 一般的な測定/単位 | 異常な動作 |
| --- | --- | --- | --- | --- |
| クラスターの健全性 | `elasticsearch_cluster_health_status` | ESクラスターの健全性ステータス | ステータス値 | 黄/赤はレプリカまたはプライマリシャードの異常を示します |
| ノード数 | `elasticsearch_cluster_health_number_of_nodes` | クラスター内のノード数 | カウント | ノード数の減少はノードがオフラインになっていることを示す場合があります |
| データノード数 | `elasticsearch_cluster_health_number_of_data_nodes` | クラスター内のデータノードの数 | カウント | データノードの減少はシャードの容量および読み書き能力に影響します |
| 保留中のタスク | `elasticsearch_cluster_health_number_of_pending_tasks` | クラスタ内の保留中タスクの数 | カウント | 継続的な増加はマスターまたはクラスタの状態更新が遅いことを示します |
| アクティブプライマリシャード | `elasticsearch_cluster_health_active_primary_shards` | アクティブプライマリシャードの数 | 個 | 減少している場合はリスクが高く、インデックスの可用性に影響を与える可能性があります |
| アクティブシャード | `elasticsearch_cluster_health_active_shards` | アクティブシャードの総数 | 個 | 減少はシャードが完全に回復していないことを示します |
| 初期化中のシャード | `elasticsearch_cluster_health_initializing_shards` | 初期化中のシャードの数 | 個 | 長時間減少しない場合は回復が遅いことを示します |
| 移動中のシャード | `elasticsearch_cluster_health_relocating_shards` | 移動中のシャードの数 | 個 | 移動が多すぎるとネットワークおよびディスクへの負荷が増加します |
| 割り当てられていないシャード | `elasticsearch_cluster_health_unassigned_shards` | 未割り当てシャードの数 | 個 | 0より大きい場合、シャードがノードに割り当てられていないことを示します |
| 遅延未割り当て | `elasticsearch_cluster_health_delayed_unassigned_shards` | 遅延未割り当てシャードの数 | 個 | ノードがオフラインになった後の再割り当てを待機中 |

よくあるクエリ: 

```promql
elasticsearch_cluster_health_status
```

```promql
elasticsearch_cluster_health_unassigned_shards
```

調査の提案: ESはまず、ヘルスステータスと未割り当てシャードを確認する必要があります。赤ステータスはプライマリシャードの処理を優先すべきで、黄色は主に未割り当てのレプリカが原因であり、長時間放置することはできません。 

### 9.2 ディスク容量とファイルシステム

| 監視次元 | メトリック | メトリクスの意味 | 一般的な測定 / 単位 | 異常なパフォーマンス |
| --- | --- | --- | --- | --- |
| データディスク合計 | `elasticsearch_filesystem_data_size_bytes` | ESデータディレクトリの総容量 | バイト | ディスク使用率を計算するために使用されます |
| 利用可能データディスク | `elasticsearch_filesystem_data_available_bytes` | ESデータディレクトリの利用可能容量 | バイト | 利用可能なスペースが不足すると、シャードの移行や書き込み制限が発生する可能性があります |

よくある質問:

```promql
(1 - elasticsearch_filesystem_data_available_bytes / elasticsearch_filesystem_data_size_bytes) * 100
```

調査の提案: ESはディスク使用量に非常に敏感です。ディスク使用量が高すぎると、シャードの移行、読み取り専用インデックス、または書き込み失敗が発生する可能性があります。インデックスの成長、保持ポリシー、ノードのディスク配分を監視する必要があります。

### 9.3 文書、インデックス、および削除

| 監視次元 | メトリック | メトリクスの意味 | 共通単位 | 異常な動作 |
| --- | --- | --- | --- | --- |
| 文書数 | `elasticsearch_indices_docs` | 現在の文書数 | 数 | 文書の急速な連続的成長には容量評価が必要です |
| 削除された文書 | `elasticsearch_indices_docs_deleted` | 削除された文書の数 | 数 | 高い削除率はマージ圧力を引き起こす可能性があります |
| インデックス書き込み回数 | `elasticsearch_indices_indexing_index_total` | インデックス操作の累積回数 | 回/秒 | 書き込みの急増は、 CPU、ディスク、およびリフレッシュの圧力を増加させます |
| インデックス書き込み時間 | `elasticsearch_indices_indexing_index_time_seconds_total` | インデックス操作の累積時間 | 秒/秒 | 書き込み時間の増加は書き込みパスを遅くする |
| 削除操作回数 | `elasticsearch_indices_indexing_delete_total` | 削除操作の累積回数 | 回/秒 | 削除の急増はセグメントマージの圧力を引き起こす可能性がある |
| 削除操作の期間 | `elasticsearch_indices_indexing_delete_time_seconds_total` | 削除操作の累積期間 | 秒/秒 | 削除期間の増加 |

よくある質問:

```promql
sum by (cluster) (rate(elasticsearch_indices_indexing_index_total[5m]))
```

```promql
rate(elasticsearch_indices_indexing_index_time_seconds_total[5m]) / rate(elasticsearch_indices_indexing_index_total[5m])
```

トラブルシューティングの推奨: 書き込みが遅いときは、書き込みだけを見ないでください QPS. リフレッシュ、マージ、トランスログ、スレッドプールの拒否、ディスクIOも考慮する必要があります。

### 9.4 クエリおよびGetリクエスト

| 監視次元 | メトリック | メトリクスの意味 | 一般的な測定/単位 | 異常な動作 |
| --- | --- | --- | --- | --- |
| クエリリクエスト数 | `elasticsearch_indices_search_query_total` | 検索クエリの累積数 | 回/秒 | クエリの急増 |
| クエリ待機時間 | `elasticsearch_indices_search_query_time_seconds` | 検索クエリの累積時間 | 秒/秒 | 平均クエリ待機時間の増加 |
| Fetch リクエスト数 | `elasticsearch_indices_search_fetch_total` | 検索フェーズでの累積数 | 回/秒 | 大きな結果セットは Fetch 回数を増加させる可能性があります |
| Fetch 待機時間 | `elasticsearch_indices_search_fetch_time_seconds` | 検索 Fetch の累積時間 | 秒/秒 | 遅い Fetch は通常、大きな結果セット、ディスク、またはネットワークに関連しています |
| Get リクエスト数 | `elasticsearch_indices_get_exists_total`, `elasticsearch_indices_get_missing_total` | Get ヒットおよびミスの累積数 | 回/秒 | ミスの増加は、存在しないドキュメントへのビジネスアクセスを示す場合があります |
| Get 所要時間 | `elasticsearch_indices_get_time_seconds`, `elasticsearch_indices_get_exists_time_seconds`, `elasticsearch_indices_get_missing_time_seconds` | Get リクエストの累積時間 | 秒/秒 | 遅い Get は、読み取りパスへの負荷増加を示します |

よくあるクエリ: 

```promql
rate(elasticsearch_indices_search_query_time_seconds[5m]) / rate(elasticsearch_indices_search_query_total[5m])
```

```promql
rate(elasticsearch_indices_search_fetch_time_seconds[5m]) / rate(elasticsearch_indices_search_fetch_total[5m])
```

トラブルシューティングの推奨事項: 遅いクエリは、クエリとフェッチを区別する必要があります。遅いクエリは、クエリ条件、インデックス構造、シャードの負荷とより関連があります。遅いフェッチは、返されるフィールドが多い、結果セットが大きい、またはディスク読み取りが遅い場合により一般的です。

### 9.5 セグメント、マージ、リフレッシュ、およびトランザクションログ

| 監視次元 | メトリック | メトリクスの意味 | 共通の指標/単位 | 異常な症状 |
| --- | --- | --- | --- | --- |
| セグメント数 | `elasticsearch_indices_segments_count` | 現在のセグメント数 | 数 | セグメント数が多すぎるとクエリやメモリに影響を与える可能性があります |
| セグメントメモリ | `elasticsearch_indices_segments_memory_bytes` | セグメントによって占有されるメモリ | バイト | 連続的な増加は圧迫する可能性があります JVM |
| マージの数 | `elasticsearch_indices_merges_total` | マージ操作の累積回数 | 回/秒 | 頻繁なマージは高い書き込みまたは削除の負荷を示します |
| マージ内のドキュメント数 | `elasticsearch_indices_merges_docs_total` | マージによって処理された累積ドキュメント数 | 件/秒 | 増加するマージ作業負荷 |
| マージデータ量 | `elasticsearch_indices_merges_total_size_bytes_total` | マージによって処理された累積データ | バイト/秒 | 大規模なマージはディスクIOを飽和させる可能性があります |
| マージ期間 | `elasticsearch_indices_merges_total_time_seconds_total` | マージに費やされた累積時間 | 秒/秒 | 遅いマージは書き込みおよびクエリ性能に影響を与えることがあります |
| リフレッシュ回数 | `elasticsearch_indices_refresh_total` | 累積リフレッシュ回数 | 回/秒 | 頻繁なリフレッシュはオーバーヘッドを増加させます |
| リフレッシュ期間 | `elasticsearch_indices_refresh_time_seconds_total` | 累積リフレッシュ時間 | 秒/秒 | 遅いリフレッシュはリアルタイム近似の可視性に影響します |
| フラッシュ回数 | `elasticsearch_indices_flush_total` | 累積フラッシュ回数 | 回/秒 | 頻繁なフラッシュはトランスログや書き込み負荷に関連する場合があります |
| フラッシュ期間 | `elasticsearch_indices_flush_time_seconds` | 累積フラッシュ時間 | 秒/秒 | 遅いフラッシュは安定性に影響を与える可能性があります |
| トランスログ操作 | `elasticsearch_indices_translog_operations` | 現在のトランスログ操作数 | 数 | 継続的な蓄積にはフラッシュへの注意が必要です |
| トランスログサイズ | `elasticsearch_indices_translog_size_in_bytes` | 現在のトランスログサイズ | バイト | 過剰なサイズは回復時間に影響を与える可能性があります |
| ストアスロットル | `elasticsearch_indices_store_throttle_time_seconds_total` | インデックスストアのスロットリングの累積時間 | 秒/秒 | スロットリングの増加、ディスクによる書き込みに影響 |

調査の提案：書き込み負荷が高い場合、マージ、リフレッシュ、フラッシュ、トランスログが同時に変化します。マージ時間とストアスロットルの増加は通常、ディスクがESに影響を与え始めたことを示します。

### 9.6 キャッシュとサーキットブレーカー

| 監視次元 | メトリック | メトリックの意味 | 共通の単位/測定 | 異常な動作 |
| --- | --- | --- | --- | --- |
| クエリキャッシュメモリ | `elasticsearch_indices_query_cache_memory_size_bytes` | クエリキャッシュによって使用されるメモリ | バイト | 過剰な使用は JVM |
| クエリキャッシュの削除 | `elasticsearch_indices_query_cache_evictions` | クエリキャッシュ削除の累積数 | 回/秒 | 頻繁な削除はキャッシュが不安定であることを示す |
| フィールドデータメモリ | `elasticsearch_indices_fielddata_memory_size_bytes` | フィールドデータによって使用されるメモリ | バイト | フィールドデータの使用量が高いと、メモリプレッシャーが簡単に発生する可能性がある |
| フィールドデータの削除 | `elasticsearch_indices_fielddata_evictions` | フィールドデータ削除の累積数 | 回/秒 | 高いクエリまたは集計の負荷 |
| フィルターキャッシュの削除 | `elasticsearch_indices_filter_cache_evictions` | フィルターキャッシュ削除の累積数 | 回/秒 | 頻繁なフィルターキャッシュの無効化 |
| ブレーカー推定サイズ | `elasticsearch_breakers_estimated_size_bytes` | サーキットブレーカーの推定メモリ | バイト | 制限に近づくとクエリが拒否される場合がある |
| ブレーカー制限 | `elasticsearch_breakers_limit_size_bytes` | サーキットブレーカー制限 | バイト | ブレーカー使用率を計算するために使用 |
| ブレーカーのトリガー | `elasticsearch_breakers_tripped` | 回路遮断器が作動した回数 | 回/増分 | 増加の説明：メモリリスクのためにブロックされたリクエスト |

よくあるクエリ: 

```promql
elasticsearch_breakers_estimated_size_bytes / elasticsearch_breakers_limit_size_bytes * 100
```

```promql
increase(elasticsearch_breakers_tripped[10m])
```

調査の推奨事項：集計クエリ、ソート、およびスクリプトクエリは、フィールドデータおよび遮断器の使用量を簡単に増加させる可能性があります。遮断器が作動した場合、通常はクエリサイズを制限し、インデックスマッピングを最適化するか、クエリ方法を調整する必要があります。

### 9.7 JVM, CPU、およびロード

| 監視次元 | メトリック | メトリクスの意味 | 一般的な測定/単位 | 異常なパフォーマンス |
| --- | --- | --- | --- | --- |
| JVM 使用メモリ | `elasticsearch_jvm_memory_used_bytes` | 現在 JVM 使用メモリ | バイト | 限界に近い状態が続き、GC圧力が増加 |
| JVM 最大メモリ | `elasticsearch_jvm_memory_max_bytes` | 利用可能最大 JVM メモリ | バイト | 計算に使用される JVM 使用量 |
| JVM コミット済みメモリ | `elasticsearch_jvm_memory_committed_bytes` | JVM コミットされたメモリ | バイト | 観察する JVM メモリ割り当て |
| JVM メモリプールピーク | `elasticsearch_jvm_memory_pool_peak_used_bytes` | 各メモリプールのピーク使用量 | バイト | 古い世代の高いピークに注意が必要 |
| GCカウント | `elasticsearch_jvm_gc_collection_seconds_count` | GCの発生回数 | 回/秒 | GCが頻繁に発生すると、レイテンシが変動する可能性があります |
| GC時間 | `elasticsearch_jvm_gc_collection_seconds_sum` | GCの合計時間 | 秒/秒 | 長時間のGCはクエリや書き込みに影響を与える可能性があります |
| プロセス CPU | `elasticsearch_process_cpu_percent` | ESプロセス CPU 使用量 | パーセンテージ | 長時間の高負荷 CPU 重いクエリや書き込みの負荷を示している可能性があります |
| システム負荷 | `elasticsearch_os_load1`, `elasticsearch_os_load5`, `elasticsearch_os_load15` | ノード 1/5/15 分ロード | ロード値 | コアより高いロードは CPU 明らかなタスクのキューイングを示します |
| オープンファイル数 | `elasticsearch_process_open_files_count` | ESプロセスによって開かれたファイルの数 | 数 | システムの制限に近づくと、インデックスファイルへのアクセスに影響を与える可能性があります |

よくある問い合わせ: 

```promql
elasticsearch_jvm_memory_used_bytes / elasticsearch_jvm_memory_max_bytes * 100
```

```promql
rate(elasticsearch_jvm_gc_collection_seconds_sum[5m])
```

調査の提案: より大きなES JVM メモリが多ければ良いわけではありません。 JVM 使用量、GC時間、フィールドデータ、クエリキャッシュ、およびブレーカーは、メモリ圧迫がクエリによるものか、ヒープサイズとデータ規模の不一致によるものかを判断するために一緒に監視する必要があります。

### 9.8 スレッドプールとネットワーク

| 監視次元 | メトリック | メトリクスの意味 | 一般的な測定/単位 | 異常なパフォーマンス |
| --- | --- | --- | --- | --- |
| アクティブスレッド | `elasticsearch_thread_pool_active_count` | スレッドプール内のアクティブスレッド数 | カウント | 長期間の高アクティブスレッドは、重い処理圧力を示します |
| 完了したタスク | `elasticsearch_thread_pool_completed_count` | スレッドプールによって完了したタスクの累積数 | 回数/秒 | 処理スループットを観察するために使用 |
| 拒否されたタスク | `elasticsearch_thread_pool_rejected_count` | スレッドプールによって拒否されたタスクの累積数 | 回数/秒 | 増加は、スレッドプールまたはキューが満杯であることを示します |
| 受信トラフィック | `elasticsearch_transport_rx_size_bytes_total` | トランスポートによって受信された累積バイト数 | バイト/秒 | ノード間通信またはリクエストトラフィックの増加 |
| 送信トラフィック | `elasticsearch_transport_tx_size_bytes_total` | トランスポートによって送信された累計バイト数 | バイト/秒 | シャードの移動、クエリ、またはレプリケーションによるトラフィックの増加 |

よくある問い合わせ: 

```promql
sum by (type) (rate(elasticsearch_thread_pool_rejected_count[5m]))
```

```promql
rate(elasticsearch_transport_rx_size_bytes_total[5m]) + rate(elasticsearch_transport_tx_size_bytes_total[5m])
```

調査の提案：スレッドプールの拒否は非常に直接的なビジネスリスクの信号です。書き込みの拒否の場合は、bulk/indexスレッドプールを確認してください。検索の拒否の場合は、searchスレッドプールを確認し、その後に CPU, JVMおよびディスクIOと組み合わせてボトルネックを特定します。

## 10. アプリケーションサービスの監視

アプリケーションの監視は、一般的なサーバーサイドのリクエスト、クライアントサイドの依存呼び出し、ランタイムリソース、共同編集のビジネスリンク、およびRSサービスのタスクをカバーします。アプリケーション指標の焦点は個々のリソースの閾値ではなく、リクエスト量、エラー、レイテンシ、および依存関係の健全性です。

### 10.1 一般的なサーバーサイドのメトリクス

| 監視次元 | メトリック | メトリクスの意味 | 共通スコープ/単位 | 異常なパフォーマンス |
| --- | --- | --- | --- | --- |
| サービス稼働時間 | `up` | アプリケーションエクスポーターまたはメトリクスエンドポイントが取得可能かどうか | `0/1` | `0` メトリクスがアクセスできない、またはサービスが異常であることを意味する |
| ビルド情報 | `ego_build_info` | アプリケーションのビルドバージョン、ブランチ、およびその他の情報 | ラベル情報 | リリースバージョンを確認するために使用 |
| 起動回数 | `ego_server_started_total` | サーバー起動の累積回数 | 回/増分 | 増加はプロセスの再起動やリリースを示す |
| サーバーリクエスト | `ego_server_handle_total` | サーバーリクエストの累積回数 | 回/秒 | リクエストの急増や急減はビジネス状況と組み合わせて判断する必要がある |
| サーバー側の時間消費 | `ego_server_handle_seconds_count`, `ego_server_handle_seconds_bucket` | サーバー側のリクエスト時間統計 | P50/P95/P99 | 遅延の増加はユーザー体験に影響する | 

よくある問い合わせ: 

```promql
sum by (service, method) (rate(ego_server_handle_total[5m]))
```

```promql
histogram_quantile(0.95, sum(rate(ego_server_handle_seconds_bucket[5m])) by (le, service, method))
```

調査の提案：サーバー側の異常については、まずリクエスト量が変化しているかどうかを確認し、その後エラーや遅延を確認してください。遅延が増加しているがリソースが高くない場合は、下流の依存関係呼び出しやキューを引き続き調査します。

### 10.2 クライアント依存関係呼び出し

| 監視の指標 | メトリック | メトリクスの意味 | 一般的な粒度/単位 | 異常な挙動 |
| --- | --- | --- | --- | --- |
| クライアント呼び出し量 | `ego_client_handle_total` | アプリケーションがクライアントとして下流を呼び出した回数 | 回/秒 | 下流呼び出し量の突然の増加、依存関係の負荷を増加させる可能性がある |
| クライアント遅延 | `ego_client_handle_seconds_count`, `ego_client_handle_seconds_bucket` | 下流呼び出しの遅延統計 | P50/P95/P99 | 下流が遅いと、現在のサービスも遅くなる可能性がある |
| クライアントの状態 | `ego_client_stats_gauge` | クライアントの接続プールまたはステータスタイプのメトリクス | 現在の値 | 接続プールの枯渇、異常なアイドル接続など |
| Kafka プロデュース遅延 | `kafka_produce_duration_seconds_bucket` | アプリケーションがメッセージを生成するのにかかる時間 Kafka メッセージ | P50/P95/P99 | ブローカーやネットワークの問題による可能性があるプロデュース遅延の増加 |

よくある質問:

```promql
histogram_quantile(0.95, sum(rate(ego_client_handle_seconds_bucket[5m])) by (le, service, target, method))
```

調査の提案: ビジネスインターフェースが遅い場合、サーバー側で消費された時間とクライアント依存関係で消費された時間を比較します。クライアントの時間比率が高い場合、対応する下流サービス、ミドルウェア、またはネットワークの確認を優先してください。

### 10.3 実行時とプロセス

| 監視次元 | メトリック | メトリクスの意味 | 一般的な標準/単位 | 異常な現れ |
| --- | --- | --- | --- | --- |
| Go の goroutine | `go_goroutines` | Go プロセス内の goroutine の数 | カウント | 継続的な増加はブロッキングまたはリークを示す可能性があります |
| Go GC の実行時間 | `go_gc_duration_seconds` | Go GC の期間 | 秒／パーセンタイル | GC 時間の増加はレイテンシに影響を与える可能性があります |
| Go ヒープメモリ | `go_memstats_alloc_bytes`, `go_memstats_heap_inuse_bytes` | Go のヒープ割り当てと使用状況 | バイト | 連続的な増加がある場合はメモリリークの確認が必要です |
| Go システムメモリ | `go_memstats_sys_bytes` | Go ランタイムがシステムから要求するメモリ | バイト | 一緒に観察 RSS |
| Go スタックメモリ | `go_memstats_stack_inuse_bytes` | ゴルーチンのスタック使用量 | バイト | ゴルーチンの増加に伴い増加 |
| Node.js GCカウント | `nodejs_gc_duration_seconds_count` | Node.js GCカウント | 回/秒 | 頻繁な GC はヒープ圧力を示す場合があります |
| Node.js GC 期間 | `nodejs_gc_duration_seconds_sum` | Node.js GC 合計期間 | 秒/秒 | GC 期間の増加は応答に影響を与える可能性があります |
| Node.js ヒープスペース | `nodejs_heap_space_size_used_bytes` | それぞれの使用状況 Node.js ヒープスペース | バイト | 制限に近い場合や連続的に増加している場合は注意が必要です |
| プロセス CPU | `process_cpu_seconds_total` | プロセス CPU 時間 | コア／秒 | 高い CPU 使用量 |
| プロセス RSS | `process_resident_memory_bytes` | プロセスの物理メモリ | バイト | 連続 RSS 成長 |
| プロセスハンドル | `process_open_fds` | プロセス内で開かれているファイルディスクリプタの数 | 数 | ハンドルリーク、接続リーク |

調査提案: Go のランタイムメトリクス Node.js 主にアプリケーションのレイテンシとリソースの増加を説明するために使用されます。アプリケーションが P95 増加すると、GC時間も同時に増加する場合、メモリアロケーションとオブジェクトのライフサイクルの確認を優先します。

### 10.4 協同編集サービス

| 監視次元 | メトリック | メトリクスの意味 | 共通単位 | 異常指標 |
| --- | --- | --- | --- | --- |
| Kafka コンシューマ遅延 | `kafka_consumergroup_lag` | 協同編集関連のコンシューマグループのバックログ | カウント | 遅延の増加はイベント処理の遅れを引き起こす可能性があります |
| プロセス時間 | `process_flow_duration_seconds_bucket` | 協同編集プロセスの時間 | P50/P95/P99 | ドキュメント協同リンクの遅延 |
| プロセス数 | `process_total` | 処理されたプロセスの総数 | 回数/秒 | 処理量の異常な変化 |
| ファイルコンテンツのサイズ | `file_content_size_bytes_bucket` | ファイルコンテンツサイズの分布 | バケット統計 | 大きなファイルの割合の増加は処理時間に影響を与える可能性があります |
| チェンジセットの期間 | `handle_changeset_cost_seconds_bucket` | チェンジセットの処理にかかる時間 | P50/P95/P99 | 編集同期遅延の増加 |
| Modoc計算回数 | `modocComputeCount` | Modoc計算の回数 | 回数/秒 | 計算量の異常な増加 |
| サーバーレス呼び出し | `serverless_invocations` | サーバーレス呼び出しの回数 | 回数/秒 | 呼び出し失敗や急増はリンクに影響を与える可能性があります |

よくあるクエリ:

```promql
histogram_quantile(0.95, sum(rate(handle_changeset_cost_seconds_bucket[5m])) by (le))
```

調査提案: 協調編集リンクの場合、 Kafka 遅延、プロセスの所要時間、チェンジセットの所要時間、ファイルサイズは一緒に確認する必要があります。大きなファイルの割合が増加すると、所要時間の増加は単一障害点によるものではなく、通常の容量負荷である可能性があります。

### 10.5 RSサービス

| 監視次元 | メトリック | メトリクスの意味 | 共通スコープ/単位 | 異常なパフォーマンス |
| --- | --- | --- | --- | --- |
| HTTP リクエスト数 | `http_requests_total` | 累積数 HTTP リクエスト | 回/秒 | リクエストの急増または急減 |
| HTTP 所要時間 | `http_requests_duration_seconds_bucket`, `http_requests_duration_seconds_sum`, `http_requests_duration_seconds_count` | HTTP リクエストの所要時間 | P50/P95/P99 | インターフェース遅延の増加 |
| gRPC リクエスト数 | `grpc_requests_total` | 累積数 gRPC リクエスト | 回/秒 | gRPC 呼び出し例外 |
| gRPC 所要時間 | `grpc_requests_duration_seconds` | gRPC リクエストの所要時間 | P50/P95/P99 | 下流または内部処理の遅延 |
| エクスポートタスクの所要時間 | `export_task_duration_milliseconds_count` | エクスポートタスク処理の数と所要時間 | ms/時間 | エクスポートタスクの処理遅延や積み残し |
| インポートタスクの所要時間 | `import_task_duration_milliseconds_count` | インポートタスク処理の数と所要時間 | ms/タスクごと | 処理の遅延や積み残しのあるインポートタスク |
| 進行中のエクスポートタスク | `export_task_in_progress` | 現在実行中のエクスポートタスク | 数 | 長時間減少しない場合、タスクが詰まっていることを示します |
| 進行中のインポートタスク | `import_task_in_progress` | 現在実行中のインポートタスク | 数 | 長時間減少しない場合、タスクが詰まっていることを示します |
| Tokio メトリクス | `tokio_metrics` | Rust Tokio ランタイムのメトリクス | 現在の値 / レート | 異常なランタイムキューまたはタスクスケジューリング |
| jemalloc メトリクス | `jemalloc` | メモリアロケータのメトリクス | バイト / カウント | メモリ断片化または割り当ての異常 |
| TCP メトリクス | `tcp` | RSサービス TCP 接続関連のメトリクス | カウント / レート | 接続圧力またはネットワークの異常 |

調査の提案: RSサービスは、オンラインリクエストだけでなく、インポート/エクスポートなどの長時間実行されるタスクも調査する必要があります。進行中のタスク数が継続的に減少しない場合、平均実行時間よりも『タスクが停止している』ことをより確実に示す傾向があります。

## 11. メトリクスの読み取りと調査の提案

### 11.1 一般的な調査順序

| ステップ | 観察項目 | 目的 |
| --- | --- | --- |
| 1 | `up`、開始時間、Pod Ready | サービスがまだ稼働しているか、最近再起動されたかを確認する |
| 2 | リクエスト量、エラー率 P95/P99 レイテンシ | 実際にビジネスに影響を与えているかどうかを判断する |
| 3 | CPU、メモリ、ディスク、ネットワーク | リソースボトルネックがあるかどうかを判断する |
| 4 | 下流依存関係のレイテンシ Kafka 遅延、遅いデータベースクエリ | 依存関係によって遅くなっているかどうかを判断する |
| 5 | リリースバージョン、構成、トラフィックの変更 | 変更に関連しているかどうかを判断する |

実際にトラブルシューティングを行う際は、最初にすべてのチャートを見るのを急がないこと。まず、"ビジネスへの影響があるかどうか"を確認し、その後で"影響がどこから来ているか"を探す。例えば、インターフェースが遅い場合、最初にアプリケーションの P95を確認し、その後クライアント依存関係のレイテンシをチェックする。依存関係が正常であれば、サービスの CPU、GC、メモリ、コンテナスロットリングを確認する。

### 11.2 一般的な例外の組み合わせ

| 症状 | 一般的なメトリックパフォーマンス | 優先調査の方向 |
| --- | --- | --- |
| インターフェースが遅い | アプリケーション P95/P99 上昇中、 CPU 高くない | 下流の依存関係、遅いデータベースクエリ、 Kafka 遅延 |
| CPU 完全に使用中 | `container_cpu_usage_seconds_total` 高い、スロットル高 | CPU 制限、ホットインターフェース、バッチ処理タスク |
| メモリ OOM | 作業セットが制限に近い、再起動回数増加 | メモリリーク、制限が小さすぎる、大きなオブジェクト処理 |
| ディスクが遅い | iowait、IOが忙しい、読み書き遅延がすべて上昇 | データベース、 Kafka, MinIO、ログ書き込み |
| ネットワーク異常 | トラフィック急増に伴うドロップ/エラー | ノード NIC, CNI、リンク、接続数 |
| Kafka 遅延 | `kafka_consumergroup_lag` 連続的に増加 | コンシューマインスタンス、消費時間、下流依存関係 |
| Redis バックプレッシャー | ヒット率低下、ミス増加 | キーの有効期限ポリシー、キャッシュ貫通、容量 |
| MySQL 遅い | 遅いクエリ、スキャン、ロック待ちが増加 | SQL、インデックス、ロック、ディスクIO |
| MinIO リスク | オフラインディスク、エラー率、容量レベルが上昇 | ディスク、ノード、バケットの増加、修復状態 |
| Elasticsearch 遅いクエリ | 検索クエリ/取得時間の増加、スレッドプール拒否の増加 | クエリ条件、インデックス構造、 JVM、ディスクIO |
| Elasticsearch 遅い書き込み | インデックス作成時間、マージ時間、ストアスロットルの増加 | 書き込みピーク、リフレッシュ、マージ、ディスクレベル |
