# nostr-media-spec

This documents seeks to create a spec for managing uploads and retrieval of media (images, audio, video) in Nostr clients. It is simple so that clients can get it working quickly with multiple media hosts (nostr.build, nostrimg, void.cat, etc.). It is designed with the assumption that it will likely be replaced or altered to the point of being unrecognizable in the future. The point of it is to get everyone on the same page with something that work, for now.

# Uploading Media

## Request

```
POST <upload_endpoint>
Content-Type: multipart/form-data; boundary=--<boundary>

--<boundary>
Content-Disposition: form-data; name="file"; filename="<filename>.<type>"
Content-Type: <mimetype>

<binary data of the file>
--<boundary>--
```

## Response

```
{
  "data": {
    "link": String?
  },
  "success": Boolean
  "status": Int
}
```

# Getting Media

## URL Schema

```
https://<hostname>/<sha256>/<filename>.<type>?<query_params>
```
Example:
```
https://i.nostrimg.com/d2f162f8b05240b19306e59143558119c572c71cdf4819c48193a5b79842b8fc/image.jpg?size=pfp
```

### SHA-256 Hash

The SHA-256 hash of the file serves three purposes.
1. Prevent duplicate files being stored, saving on storage costs.
2. Allow clients to warn users if the file has been altered from it's original state.
3. Give URLs a *Bitcoin aesthetic*.

[Example Implementation](https://codepen.io/dulldrums/pen/RqVrRr)

### Filename (optional)

Since the file can be identified by it's hash, media hosts can implement a feature that lets users change the name of their file without having to do anything.

Example:
1. Server stores file as /\<sha256\>/_.jpg
2. User requests /\<sha256\>/thisisfine.jpg
3. Server returns /\<sha256\>/_.jpg

### Type (optional)

Similar to filenames, since files are identified by their hash, media hosts can implement on-the-fly type conversion, if they wish.

### Potential Query Params (optional)

Clients/users can append optional query parameters to make on-the-fly edits of the original file. Hosts can cache these altered versions for however long they feel is necessary to minimize compute/storage costs. This helps clients to optimize performance of their apps. The original file is retained "forever".

- width: The target width of the image/video
- height: The target height of the image/video
- size: The target size of the image/video, based on defined presets
  - pfp
  - banner
  - thumbnail
  - full
- ar: The aspect ratio of the image/video, based on defined presets
  - 1:1
  - 3:2
  - 16:9
  - etc.
