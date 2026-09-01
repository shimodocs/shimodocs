# General Tools

[← ShimoDocs Suite Deployment Documentation](../../../README.md)

## 1. Page Overview

The General Tools page includes 7 commonly used functions: JSON formatting, format conversion, JWT decoding, timestamp conversion, machine time check, QR code parsing, and Base64 encoding/decoding.

1. Click any function card to enter the operation page.
2. Once inside, you can switch to other tools directly from the function list on the left.
3. Click "Return to Function Menu" to go back to the card homepage.

## 2. JSON Formatting

This function is used to format, compress, and validate JSON content.

1. Click "JSON Formatting".
2. Enter the content to be processed in the "Input JSON" area, for example:

   ```json
   {"name":"MDP","enabled":true,"items":[1,2]}
   ```

3. Click "Format," and then indenting JSON will be generated on the right.
4. Click "Compress," and after compression, JSON without spaces and line breaks will be generated on the right.
5. Click "Copy" to copy the processed result.
6. Click "Clear" to delete the input and output content.

Test results: Strings, boolean values, and arrays are all correctly preserved, and the formatting and compression functions work properly.

## 3. Format Conversion

This feature supports conversion and formatting between different formats: JSON, YAML, and TOML.

1. Click "Format Conversion."
2. In "Source Format," select the format of the input content.
3. In "Target Format," select the desired output format.
4. Enter the content to be converted on the left.
5. Click "Convert," and the result will be displayed on the right.
6. Click "Swap Formats" to switch the source and target formats.
7. Click "Format" to adjust the indentation and layout of the current content.
8. Click "Copy" to copy the output result.

This time we are converting JSON to YAML, input:

```json
{"name":"MDP","ports":[80,443],"enabled":true}
```

Conversion result: 

```yaml
name: MDP
ports:
  - 80
  - 443
enabled: true
```

Measurement results: Fields, arrays, and boolean values are all correctly converted.

## 4. JWT Decoding

This feature is used to parse the Header, Payload, and Signature of a JWT token.

1. Click "JWT Decoding."
2. Paste the JWT token into the input box.
3. Click "Decode."
4. View the signature algorithm and Token type in the Header.
5. View information such as user, role, and expiration time in the Payload.
6. View the original content of the Signature.
7. Click the copy button for each part to copy the parsing results.
8. Click "Clear" to delete the current Token and parsing results.

Test results: The test token successfully parsed fields such as user, role, and expiration time. `HS256`, `JWT`

> JWT decoding is only used to view the Token structure and cannot replace server-side signature validity verification.

## 5. Timestamp Conversion

This function supports bidirectional conversion between Unix timestamps and date-time.

### 5.1 Convert Timestamp to Date/Time

1. Click "Timestamp Conversion".  
2. Enter a 10-digit second or 13-digit millisecond timestamp in the "Timestamp (seconds or milliseconds)" field.  
3. Click "Convert" at the top.  
4. View the date and time in "Conversion Result".  
5. Click the copy button next to the result to copy the content.  

### 5.2 Convert Date/Time to Timestamp

1. Enter the time formatted as 'YYYY-MM-DD HH:mm:ss' or ISO in the "Date/Time" field.  
2. Click "Convert" below.  
3. View the Unix timestamp in "Conversion Result (seconds)".
4. Click "Current Time" to quickly fill in the current timestamp and date. 
5. Click "Clear" to clear all content.

Test result: '1704067200' has been successfully converted to date and time, and the date and time can also be correctly converted back to a timestamp in seconds.

> When checking cross-timezone data, first clarify whether the business time uses UTC or the local timezone.

## 6. Machine Time Check

This feature is used to check the time of all Pods in the current `app=ws-gateway` NAMESPACE and highlight instances with a time deviation exceeding 30 seconds.

1. Click "Machine Time Check".
2. Click "Refresh" in the upper right corner.
3. Check the current NAMESPACE and query the labels.
4. View the system-calculated reference time, i.e., the median time of all Pods.
5. View the node each Pod is on, the Unix timestamp, and the readable time.
6. Check the "Difference from Reference" and "Status".
7. If the deviation exceeds 30 seconds, check the node's NTP/Chrony, virtual machine time, and time zone settings.

Test results: 1 `ws-gateway` Pod returned, reference time deviation is `0s`, status is "Normal".

## 7. QR Code Analysis

This feature allows you to upload QR code images and extract the text, links, or other content contained within.

1. Click "QR Code Analysis".
2. Click "Choose File".
3. Select a clear QR code image from your local device.
4. After the page displays the image preview, check the "Analysis Result" below.
5. Compare the result with the expected content of the QR code to confirm consistency.
6. Click "Copy" to copy the analysis content.
7. Click "Clear" to delete the image and analysis result.

Test results: The test QR code can be successfully uploaded, previewed, and correctly analyzed.

## 8. Base64 Encoding and Decoding

This feature is used for two-way conversion between plain text and Base64 content.

### 8.1 Base64 Encoding

1. Click on "Base64 Encode and Decode".
2. Enter plain text on the left side.
3. Click "Encode".
4. View the Base64 encoded result on the right side.

### 8.2 Base64 Decoding

1. Enter Base64 content on the left side.
2. Click "Decode".
3. View the restored text on the right side.
4. Click "Copy" to copy the result.
5. Click "Clear" to clear the input and output.

Test results:

```text
MDPTEST → TURQ5rWL6K+V
TURQ5rWL6K+V → MDPTEST
```

Chinese UTF-8 content can be converted back and forth normally.

