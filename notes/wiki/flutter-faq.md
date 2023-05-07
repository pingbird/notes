---
share: true
title: "Flutter FAQ"
---

## HTTP

### Downloading files with progress

The best way to download a file is to use [File.openWrite](https://api.dart.dev/stable/3.0.0/dart-io/File/openWrite.html) and a streamed request. This allows the file to be read in chunks instead of all at once, saving you from dangrous out of memory errors:

```dart
import 'dart:io';  
  
import 'package:http/http.dart';

Future<void> download(
  File file,
  StreamedResponse response, {
  void Function(int, int?)? onProgress,
}) async {
  final sink = file.openWrite();
  if (onProgress == null) {
    await sink.addStream(response.stream);
  } else {
    var bytes = 0;
    await sink.addStream(response.stream.map((e) {
      bytes += e.length;
      onProgress(bytes, response.contentLength);
      return e;
    }));
  }
  await sink.flush();
  await sink.close();
}

void main() async {
  final request = Request('GET', Uri.parse('https://i.imgur.com/img0gF3.jpg'));
  final response = await request.send();
  final file = File('birb_slenp.jpg');
  await download(file, response, onProgress: (bytes, total) {
    // Some servers don't send a Content-Length
    if (total != null) {
      print('${(100 * (bytes / total)).round()}%');
    }
  });
}
```

### Uploading files with progress

Uploading is a similar story, we use [File.openRead](https://api.dart.dev/stable/3.0.0/dart-io/File/openRead.html) to read the file and a [StreamedRequest](https://pub.dev/documentation/http/latest/http/StreamedRequest-class.html) to pipe it to the server in chunks:

```dart
import 'dart:async';
import 'dart:convert';
import 'dart:io';

import 'package:http/http.dart';

Future<StreamedResponse> upload(
  File file,
  StreamedRequest request, {
  void Function(int, int)? onProgress,
}) async {
  final stream = file.openRead();
  final total = file.lengthSync();
  final sink = request.sink as StreamSink<List<int>>;
  request.contentLength = total;
  if (onProgress == null) {
    unawaited(stream.pipe(sink));
  } else {
    var bytes = 0;
    unawaited(stream.map((e) {
      bytes += e.length;
      onProgress(bytes, total);
      return e;
    }).pipe(sink));
  }
  return await request.send();
}

void main() async {
  final request = StreamedRequest(
    'POST',
    Uri.parse('http://localhost:621/upload'),
  );
  final file = File('birb_slenp.jpg');
  request.headers['content-type'] = 'image/jpeg';
  final response = await upload(file, request, onProgress: (bytes, total) {
    print('${(100 * (bytes / total)).round()}%');
  });
  print('statusCode: ${response.statusCode}');
  print('body: ${utf8.decode(await response.stream.toBytes())}');
}
```

### Multipart request progress

Again same as before, we can use openRead and map to count the bytes:

```dart
MultipartFile multipartFileWithProgress(  
  String field,  
  File file,  
  void Function(int, int) onProgress,  
) {  
  final total = file.lengthSync();  
  var bytes = 0;  
  return MultipartFile(  
    field,  
    file.openRead().map((e) {  
      bytes += e.length;  
      onProgress(bytes, total);  
      return e;  
    }),  
    total,  
  );  
}
```

### Error handling

For error handling I much prefer using a custom exception class to cut down boilerplate and make it easier to surface useful errors to the frontend: https://github.com/PixelToast/puro/blob/a2d11907895b4334a867d51145cb8e325c0c9d0b/puro/lib/src/http.dart#L174

After every request you would call `HttpException.ensureSuccess(response)` which throws a formatted exception when the response does not have a 2xx status code.