# Configuration du stockage d'objets

[← ShimoDocs Suite documentation de déploiement](../../README.md)

Ce document est destiné à guider le personnel de mise en œuvre, d'exploitation ou d'intégration pour compléter la ShimoDocs connexion à un tiers externe S3 stockage d'objets étape par étape.


# 1. Confirmation préopératoire

## S3 Exigences de stockage d'objets

`Only object storage fully compatible with the S3 protocol is supported. Huawei Cloud OBS, Alibaba Cloud OSS, Tencent Cloud COS, and AWS S3 are recommended. For local deployment, MinIO can be considered.`



> [!TIP]
>
> Préférence pour le cloud public, doit prendre en charge HTTPS l'accès externe



## Connectivité réseau

Les ports pour le K8s Le réseau de cluster d'entreprise pour se connecter à l'instance de stockage d'objets doit être ouvert

```js
telnet IP 9000
```
## Contrôle d'accès et permissions
- Fournir les informations d'authentification AK/SK complètes
- Doit prendre en charge pleinement les interfaces principales telles que PutObject, GetObject, DeleteObject, ListObjects, CopyObject, InitiateMultipartUpload



# S3 Description des exigences de stockage
- Exigences de latence : dans un environnement de réseau interne, le temps de réponse moyen du stockage API est recommandé d'être < 50 ms ; dans un environnement de réseau public, il est recommandé d'être < 200 ms. Une latence élevée affectera directement la vitesse d'ouverture des documents et l'expérience de téléchargement des pièces jointes.
- Capacité de concurrence : doit prendre en charge le pic estimé de l'entreprise QPS. ShimoDocs générera un trafic de rafale lors de la collaboration multi-utilisateurs et de l'import/export par lots, donc le côté stockage ne doit pas avoir de politiques de limitation de débit trop strictes.
- Disponibilité SLA: Il est recommandé que la disponibilité du stockage dans l'environnement de production soit ≥ 99,9 %.
- La communication du stockage sur le réseau public doit se faire par HTTPS/TLS canaux cryptés.
- Synchronisation temporelle : le S3 service de stockage et le ShimoDocs serveur d'application doivent se synchroniser via NTP, sinon S3 la vérification de la signature échouera.
