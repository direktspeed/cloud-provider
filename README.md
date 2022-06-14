# cloud-provider
A Gui Interface for Maintaining/Deployment/Management of own GAIA-X Implementations and Services.

Contains also the needed API Dependencies this is a MonoRepo to allow easy forking and Adoption
as also white label the parts if needed. You are always free to ask for white label tools and services via email: frank+cloud-provider@dspeed.eu

you can use the above email for all contact needs. A Representativ or myself will investigate as fast as possible.

It is maybe importent to say that this is using the [https://github.com/internet-of-presence/](https://github.com/internet-of-presence/IoP) Transport Layer with additional compat to the existing experimental implementations like HTTP3

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
  - HTTP2 Including downgrade should only get used to Init WebRTC if you got no HTTP3 Endpoints.
- Cloudflare Implementation Gets used. HTTP3 Specs Implemented in Rust
- Memcached + KAFKA or Similar Transactional BUS needs to get used or Couchbase with the Correct Algorythms depending on your Scaling needs
  - Message Bus Logs need to get storred in Raw Format and need to get retained for Audits at last 9 Month Retention Period. (The 9 Month got carefully choosen)
  - Persistent Transactional Data can be in fleece/Couchbase/Kafka/git Format other formats are not supported as of time of writing. 
- Websocket Implementations are Forbidden! Use HTTP Long Pooling with socket.io if you need so!
  - Also carefull designed as this is a HTTP1/1 feature so we deprecated that!
  - switch to WebRTC
- Internal Communication https://github.com/capnproto/capnproto a grpc succesor!
- gRPC is Supported for Migration and has 10+ Years Support
- OpenStack API's are supported for Migration and will have even 10+ Years Support
- Kubernetes API's are supported for Migration and will have even 10+ Years Support
- MesOS is hard deprecated and replaced by this package directly as this allows you to setup MesOS like Tasks via the cloud-provider/transaction package
- to be continued.

## Hosting Legacy Content
To support Legacy Content HTTP/2 with Downgrade to HTTP/1.1 is Allowed while Websockets should get still deprecated asap and replaced by WebRTC
This Eliminates the Needs for Loadbalancers as the WebRTC Endpoints do Handle the Client Communication see: ./security/* for Security Impact. Ask us Private for Mitigation Strategies if needed. via email frank+security-cloud-provider@dspeed.eu 

## Sponsors
The below listed Sponsor do Contribute Interlectual Property and drive the Standard.
- Apache Foundation - Many good Open Source Projects
- CNCF - Kubernets API Design and go implementation, As also Many good Open Source Projects.
- Oracle GraalVM - Quarkus - ES4X special thanks as they gave us a total new way to run 30+ Coding Languages in the Cloud. 
- Eclipse Foundation - VertX: => ES4X - Quarkus, Thaia IDE 
- Microsoft - Github, Windows, Diverse Contributions to Open Source Projects we depend on, And Many More. 
- Google - Android - Chrome - HTTP3- GRPC, Borg, Goma Compiler for chromium and many more!
- Cloudflare - Interface Design and HTTP3
- Couchbase - fleece data structure inspiration memcached improvements
- DIREKTSPEED - Composition review Experiments the whole science behind this Project. 
- Frank Lemanschik - https://github.com/frank-dspeed Main Engineer driving this forward
- Murat Dogan - https://github.com/murat-dogan World Leading WebRTC Science and Engineering
- Ryan Dahl - https://github.com/ry Like Minded Thinker v8 rust binding pioneer and c++ :) 
- Chromium - Platform and Source Code Composition as also Security and Research and so much more without them this would not be possible.
- All Other Contributors Affiliates and People you know who you are! We will Try to work on a nice visual Graph stay tuned for that!

## Importent Readup why we do this for free
https://ar.al/2020/01/01/in-2020-and-beyond-the-battle-to-save-personhood-and-democracy-requires-a-radical-overhaul-of-mainstream-technology/
Our Part of the fight for freedom is that we know the fact that if we do not Create our own Gaia-X Standard they will do it like they always did it
with bad design intend and everything that we need to leave behind us to get our Autonomy Back as a Person as a State as a Group as Everything we are but we are not other peoples dog food!

# Direct Message to the EU!!
1. Stop investing in startups and acting like Silicon Valley’s unpaid research and development department and invest in stayups instead.

2. Create a noncommercial top-level domain (TLD) open to anyone in the world where anyone can register a domain name (with an automatic Let’s Encrypt certificate) for zero cost with a single API call.

3. Build upon the previous step to offer every EU citizen, paid for by EU taxpayer money, a basic virtual private server with a basic amount of resources to host an always-on node in a peer-to-peer system that would unshackle them from the Googles and Facebooks of the world and create new opportunities for people to communicate privately as well as to express political will in a decentralised manner.

And, in general, at the very least, it’s time for every one of us to pick a side. You Can use this your Self as Person to get The Control who owns your Data Back!

The side you pick will decide whether we live as people or as products. The side you pick will decide whether we live in a democracy or under capitalism.

## Finally

#### Install

##### Snapd (confined-desktop-shell)

##### npm package (development and systemwide deployment)

#### Usage

##### Snapd (confined-desktop-shell)

##### npm package (development and systemwide deployment)
