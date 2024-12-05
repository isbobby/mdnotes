# Overview
Live streaming system differs from other systems from the **data** it's working with. It's working with **multimedia data** which has slightly different protocols. It can be broken down into three main stages - streamer upload, server ingestion, viewer download and viewing.
# System Design
## Multimedia Streaming
The first challenge is to provide efficient write mechanism for streamers. The best protocol for streamers to upload would be **RTMP** (real time messaging protocol), where it's established over a persistent TCP connection. Its characteristics are
1. reliable (TCP), as compared to UDP
2. performant (1-3 seconds latency), as compared to HTTP (5-30 seconds)
3. Not scalable, where as HTTP is very scalable, and UDP is scalable with multicast

An alternative multimedia protocol is Web RTC - Web RTC uses UDP as underlying protocol, providing sub-second latency, and allows bi-directional communication, making it ideal for video call. However, one-to-many broadcast is challenging as it's a peer to peer design.

After selecting RTMP as the protocol, the data is not immediately available where it requires re-packaging before sending it to the CDNs.
## Server Ingestion
When the multimedia reaches the streaming server, assuming RTMP is used, we would need to transcode the data.

RTMP messages are categorised into **control, media, and data messages**.
1. Control message initiates the handshake 
2. Media message contains the encoded media data
3. Data message contains the metadata

**Control Message** 
This notifies the server a stream is about to start, server can publish message to topics (followers)

**Data Message** 
This contains the stream metadata, which allows the server to certain level of search indexing, see [[Distributed Search Engine System Design]].

**Media Message**
contains the actual content, the data in this form cannot be ingested by CDN directly and has to be decoded. It's usually decoded into `time_stamp : [[frame], [audio]]` format, the server can then bundle the data up into short duration (2-10s) segments for faster delivery to CDN.

Since RTMP does not naturally adapt streams for CDNs, the server may also encode the stream into multiple bitrates and formats.

After all processing and indexing is done, the server will re-encode the data into HLS / DASH for CDN delivery.
## CDN Population
### HLS
HTTP Live Streaming (HLS) was developed by Apple, used primarily for delivering video and audio content over the internet.

HLS requires the video file to be divided into smaller chunks, with an index file listing all these segment and their URLs. The index file and their segments are then distributed across multiple CDN servers located in various regions. CDNs cache the HLS segments closer to the users.

If cases of cache miss, the CDN will have to request for the missing segment or index file from the origin. This can be mitigated with a multi-tier CDN structure.
### DASH
DASH is a similar alternative to HLS, it's an open source protocol and supports more non-apple devices.
### Manifest Behaviour
Depending on the behaviour supported by the platform, we may not need to provide full playback capabilities (allowing the user to rewatch from the beginning). For such use cases, we can dynamically update the manifest to include the latest few segments while dropping the older ones (sliding window).
### CDN Caching
While storing segments in various bit rates, we want to minimise file name changes (make it append only), as well as using cache friendly names by omitting `?, &, #, ' '`.  Since the manifests may change dynamically.
### Multi-Level CDN
For better latency, we can use a multi-level CDN to minimise cache misses, where we categorise CDN level into edge, central, and origin. User will always attempt to request data from the edge first. The platform server will need to propagate changes to the origin CDN servers, who will then propagate the changes down.
## Client Loading
From the above set up, we conclude the interface between client and the multimedia content is the manifest file residing in CDN. The first step would be propagating streaming information to the viewing client, either through push notifications (web socket), or client initiated searches.

After obtaining the streaming metadata, client would have to retrieve the streaming manifest URL and use it to query against CDN to get the initial segments to start playback window, using either DASH or HLS.

Client starts playback while continuously polling CDN for new segments. If the client requires enriched experience (get real time comments, likes etc), the client would likely need to establish a websocket connection with an edge platform server.
![[live_stream_system_design_basic.png]]