# cloud-provider
A Gui Interface for Maintaining/Deployment/Management of own GAIA-X Implementations and Services

## DIREKTSPEED Cloud Provider
DIREKTSPEED Aims to draft the v1.0 Specs for GAIA-X while we do not even attend to the group as the Companys that are in that domain are far behind us in terms of Knowleg Gathering about Secure Cloud Services and how they work and get Maintained.

So we needed to give them the Right Tools to Make GAIA-X real we Open Source Our knowleg so that it scales and can get addopted by Them fast.
We do that without any Profit Intend we do it because it Needs to get Done and we do it because we Can do It.

## How Does this work?
The @stealify/cloud-provider package is using the @stealify/platform (chromium++) to Offer Unified Desktop and Mobile Device Expirences as also to access
GAIA-X Resources from diffrent Providers. This way we do not need to use the Original Provider API's and we can even implement our own API over the one from the Provider that reduces the convergences. 

## GAIA-X Interface Specs v1.0.0 (Draft Stage 3)
- Quic/UDP Transactional + Transaction nonce
- No HTTP < 3 and no HTTP without the S at the END! Only in Case of Binary and Dev
- Cloudflare Implementation Gets used. HTTP3 Specs Implemented in Rust
- Memcached + KAFKA or Similar Transactional BUS needs to get used or Couchbase with the Correct Algorythms depending on your Scaling needs
  - Message Bus Logs need to get storred in Raw Format and need to get retained for Audits at last 9 Month Retention Period. (The 9 Month got carefully choosen) 
- Websocket Implementations are Forbidden! Use HTTP Long Pooling with socket.io if you need so!
  - Also carefull designed as this is a HTTP1/1 feature so we deprecated that!
- Internal Communication https://github.com/capnproto/capnproto a grpc succesor!
- gRPC is Supported for Migration and has 10+ Years Support
- Persistent Transactional Data can be in fleece/Couchbase/Kafka/git Format other formats are not supported as of time of writing. 
- OpenStack API's are supported for Migration and will have even 10+ Years Support
- Kubernetes API's are supported for Migration and will have even 10+ Years Support
- MesOS is hard deprecated and replaced by this package directly as this allows you to setup MesOS like Tasks via the cloud-provider/transaction package
- to be continued.

## Hosting Legacy Content
To support Legacy Content HTTP/2 with Downgrade to HTTP/1.1 is Allowed while Websockets should get still deprecated asap and replaced by WebRTC
This Eliminates the Needs for Loadbalancers as the WebRTC Endpoints do Handle the Client Communication see: ./security/* for Security Impact. Ask us Private for Mitigation Strategies if needed. via email frank+security-cloud-provider@dspeed.eu 
