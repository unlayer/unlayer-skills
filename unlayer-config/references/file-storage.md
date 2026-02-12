# File Storage — Detailed Reference

## Custom Image Upload with Progress (XHR)

```javascript
unlayer.registerCallback('image', function (file, done) {
  var xhr = new XMLHttpRequest();

  xhr.upload.onprogress = function (e) {
    done({ progress: Math.round((e.loaded / e.total) * 100) });
  };

  xhr.onload = function () {
    var result = JSON.parse(xhr.responseText);
    done({ progress: 100, url: result.url });
  };

  xhr.onerror = function () {
    console.error('Upload failed');
  };

  var data = new FormData();
  data.append('file', file.attachments[0]);
  xhr.open('POST', '/api/uploads');
  xhr.send(data);
});
```

## File Manager — Custom Database Provider

```javascript
unlayer.registerProvider('userUploads', function (params, done) {
  var page = params.page || 1;
  var perPage = params.perPage || 20;
  var searchText = params.searchText;

  fetch(`/api/images?page=${page}&perPage=${perPage}&search=${searchText || ''}`)
    .then((r) => r.json())
    .then((data) => {
      var images = data.items.map((img) => ({
        id: img.id,
        location: img.url,
        width: img.width,
        height: img.height,
        contentType: img.contentType,  // 'image/png', 'image/jpeg', etc.
        source: 'user',
      }));
      done(images, {
        hasMore: data.total > page * perPage,
        page: page,
        perPage: perPage,
        total: data.total,
      });
    });
});
```

## Handle Image Deletion

```javascript
unlayer.registerCallback('image:removed', function (image, done) {
  // image: { id, userId, projectId }
  fetch(`/api/images/${image.id}`, { method: 'DELETE' })
    .then(() => done());
});
```

## Amazon S3 Setup

Configure in Unlayer dashboard: **Settings > Storage**.

Required S3 CORS configuration:

```json
[
  {
    "AllowedHeaders": ["*"],
    "AllowedMethods": ["GET", "PUT", "POST", "DELETE"],
    "AllowedOrigins": ["https://*.unlayer.com"],
    "ExposeHeaders": []
  }
]
```

**Steps:**
1. Create S3 bucket (uncheck "Block all public access")
2. Create IAM user with programmatic access
3. Attach policy with minimum required permissions
4. Add CORS configuration above
5. Add credentials in Unlayer dashboard (Settings > Storage)
6. Test the configuration

> Docs: https://docs.unlayer.com/builder/file-storage
> Docs: https://docs.unlayer.com/builder/file-storage/amazon-s3
> Docs: https://docs.unlayer.com/builder/file-storage/custom
> Docs: https://docs.unlayer.com/builder/file-manager
