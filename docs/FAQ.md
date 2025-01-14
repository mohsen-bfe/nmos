# NMOS FAQ
{:.no_toc}

- A markdown unordered list which will be replaced with the ToC, excluding the "Contents header" from above
{:toc}

## What are the Networked Media Open Specifications / NMOS?

The Networked Media Open Specifications (NMOS for short) support professional AV media industry’s transition to a “fully-networked” architecture, by providing an interoperable approach to discovering, connecting and managing resources.

NMOS specs are required by other industry recommendations, in particular [TR-1001-1][], which addresses requirements for the behaviour of media devices and the network enviroment in which devices run.

## Why is NMOS needed?

SMPTE [ST 2110][] specifies how to transport and synchronise video, audio and ancillary data as RTP streams. But it doesn't cover how to discover or connect the devices that send and receive these streams.
This leaves a risk that multiple proprietary approaches will proliferate, making interoperability more difficult and limiting what can be done with IP-based facilities.

NMOS provides open and interoperable solutions to avoid this.

## Will NMOS do everything we need?

**No**: it specifically addresses certain requirements in the stack (or "[pyramid][EBU-Tech-3371]" of technologies that the networked media industry need, and does so in an open and interoperable way.

In some cases AMWA may publish new or updated specifications to provide extended or new functionality, such as confirmation that certain IS-06 actions have been taken. While in other cases AMWA may decide not to address new functionality.

AMWA takes decisions of what to work on based on demonstrable business need of its members.

In any case, any real solution will depend on other best practice network services such as DNS and DHCP. (See also question below about [TR-1001-1][].)

## Aren't there other things that do what NMOS does?

In some cases yes. There are vendor-specific networked media products that include discovery and connection capabilities.  But these are proprietary rather than open and interoperable.

## What form do the NMOS specifications take?

The NMOS specifications provide a set of building blocks for accessing and working with networked media resources (Node, Device, Sender, Receiver, etc.) as modelled in the [JT-NM Reference Architecture][].

NMOS provides API and schema definitions for how these resources are represented and accessed in the control plane. As appropriate, these use a RESTful or publish-subscribe approach.

The various NMOS specifications are written in a way that ensures that they are mutually compatible, and extensible. This is aided through by use of components across the IS's, including:

- Common API semantics and syntax
- Common approach to versioning
- Common approach to extension through [Parameter Registers][]
- Shared data and security models
- Consistent use of GitHub repositories, documentation, testing and continuous integration

## What are IS-04, IS-05, IS-06, etc?

These are identifiers assigned by AMWA for Interface Specifications they create, including the NMOS specs so far, starting with IS-04 for Discovery and Registration.
See the [NMOS Documentation][] page for the full list.

AMWA has other types of specifications such as "AS" is used for Application Specifications and "MS" for Data Model Specifications, and "BCP" is used for Best Current Practice.

For more detail on AMWA's specification process see [BCP-001][].

## Why not just have a single NMOS Specification/API?

NMOS has multiple APIs because it includes multiple small pieces of functionality that provide building blocks to solve specific problems. So connecting two audio devices could involve functionality from IS-04, IS-05 and IS-06. Bundling everything into one monolithic specification/API is considered an anti-pattern, and gives rise to maintainability, scalability, interoperability and roadmap issues.

## Where can I find the NMOS specifications?

Go to the main [NMOS Documentation][] page and follow the links to the required specifications.

For each specification, you will find:

- A description of what it does, why it's needed, how it works
- An index of documentation
- Lists of APIs, schemas and examples
- Links to different development versions and releases

## Is NMOS a product?

**No**. Vendors choose to build NMOS into their products, and users choose products that support NMOS.

## Where can I find products that support NMOS?

[This page][NMOS-Solutions][] lists many products supporting IS-04 and IS-05 and other NMOS specifications.

See also the [JT-NM Tested][] TR-1001 Catalogue.

## Are there open source implementations?

**Yes**, from a variety of sources, mostly using the Apache 2.0 license. They are listed on the [above][NMOS-Solutions] page.

## How are implementations tested?

AMWA's open source [NMOS Testing][] tool provides simple web service for testing NMOS API implementations.
It downloads and parses the specification to automatically create and run basic tests to check API syntax etc. We add manual tests to check specific functionality for each specification.

The testing tool is useful in four ways:

- It lets manufacturers test their implementations as they are developing them.
- It helps save time during AMWA Incubator workshops.
- It is used formally for JT-NM NMOS/TR-1001 testing (see below).
- It helps identify interoperability issues between vendors.

## How are implementations certified?

AMWA does not currently certify products. However the [JT-NM Tested][] programme covers IS-04 and IS-05 support for media devices within its TR-1001 testing, and maintains a catalogue of tested products. This will soon be extended to include testing of IS-08, and of registries and clients.

The [NMOS Testing][] tool is used for this programme.

## When will IS-xx / NMOS be standardised? When is it “done”?

The [NMOS Documentation][] page gives the current status of the Specifications.
You will see that many are already published. You will also see that some have multiple versions.
As the professional networked media industry matures we can expect requirements to keep changing, so although individual versions will be "done", NMOS will not – and cannot – stand still.

This is commonplace with software products, and reflects the changing nature of the industry.
However maintaining compatibility is important, and to date all changes introduced have been non-breaking.

NMOS's API versioning semantics allow [different versions of implementations to work together][Upgrade Path], so that new systems can be used alongside older ones without things breaking; users will only need to upgrade to take advantage of new features.

[TR-1001-1][] specifies use of IS-04 version 1.2 _or higher_ and IS-05 version 1.0 _or higher_.

## How can IS-04 be used in a scalable and resilient way?

IS-04 defines the Registration and Query APIs, and how to find their endpoints. But it does not define how the API backend should be implemented, nor does it define how the registry data should be stored. This allows implementers and integrators to choose an approach to scaling each of these that is appropriate to topology of the infrastructure and the usage scenarios. There are a wide range of techniques available from how web APIs and databases can be implemented that can be used.

The design of IS-04 includes a number of features to aid scalability and resilience:

- A heartbeat mechanism allows a Node to know quickly when a registry instance has failed. The Node can dynamically switch between available registry instances upon failure, with zero downtime.
- A priority mechanism allows specific APIs to be favoured.
- The Query API provides filtering mechanisms, including pagination and basic/advanced query languages to help minimise query traffic.

Also, IS-05 supports bulk connections, which reduces the network overhead when many changes are made togegther.

Some details on the sorts of considerations which should go into scaling to very large systems is included in the End User Guides on the [wiki][NMOS Wiki], and [this presentation][Scalability] outlines results from a Networked Media Incubator study testing the performance of IS-04 and IS-05 at scale.

## Why and how does NMOS use DNS-SD? What does this mean for scalability?

The first thing to point out is that DNS-SD plays a relatively minor role in NMOS. It helps minimise the amount configuration required by automating how to discover API endpoints:

- Nodes use DNS-SD to find the IS-04 Registration API endpoints
- Clients use DNS-SD to find IS-04 Query, IS-05 Connection and other API endpoints.

Most NMOS traffic takes the form of HTTP/WebSocket API messages (also MQTT can be used for IS-07 event messages), and the choice of DNS-SD does not impact on scalability.

## What about mDNS?

DNS-SD can be either multicast or unicast. Multicast DNS-SD (aka mDNS) provides a suitable approach for small setups, including when using peer-to-peer mode discovery. Unicast DNS-SD is preferred for larger deployments where an IS-04 registry is used (and multicast Node announcements were deprecated for registered mode in IS-04 v1.3.)

Unicast DNS-SD is mandatory in [TR-1001-1][] environments.

## Does NMOS require multicast?

**No.** See above. Furthermore, although IS-04/05 are often used with ST 2110 multicast streams they are not limited to these.

## Does NMOS only support RTP?

**No.** The transport used for Flows is independent of how the Flows are discovered and how connections are made.

We already use IS-05 to connect IS-07 WebSocket/MQTT event flows, and Streampunk's [Arachnid][] is an example of how Grains may be transported over HTTP(S), using HTTP headers to carry identity, timestamp and other Grain attributes.

## Does NMOS only support ST 2110? Or only uncompressed?

**No.** See above.

## What is TR-1001-1?

[TR-1001-1][] is a technical recommendation from the [JT-NM][], covering many aspects of how ST 2110, PTP and NMOS should be used for "engineered networks" such as those suitable for media facilities. It makes some constraints on the use of these standards and specifications and also requires certain network services to be available, including DNS, DHCP and LLDP, and defines certain startup and failover behaviour.

TR-1001-1 could be considered as including a "profile" for the use of IS-04 and IS-05 for such facilities.

## Does IS-04 still support peer-to-peer discovery (i.e. without a registry)?

Yes. Although TR-1001-1 requires the use of registered mode, this is still useful for small ad-hoc cases.

## Is there more to IS-07 than just tallies?

Although its title refers to "Event and Tally", IS-07 can be used to carry any type of time-related data payload.  For instance at the IBC 2019 IP Showcase we demonstrated a control panel that generates IS-07 events with information about the state of its buttons and dials, with several receiving devices subscribing to these event flows. This potentially could form the basis of an open approach to control transport.

## What is a "Broadcast Controller" and how does it relate to NMOS?

"Broadcast Controller" is typically used for a system that tells items of broadcast equipment what to do. In a traditional studio facility this might include sending control commands to an SDI router or to a bank of studio monitors. A number of propriatry control protocols are used in the industry (recently network protocols but older equipment may use serial ports etc.), and many broadcast controllers can "speak many languages", and integrate with multiple vendors' APIs. Several AMWA members make such systems.

A broadcast controller can support NMOS by including clients for NMOS APIs. So:

- An IS-04 client could find Senders and Receivers
- An IS-05 client could make/break connections that the controller manages
- An IS-06 client could make sure the network layer is ready for the connections
- An IS-07 client could accept incoming event information that the controller needs to know about
- An IS-08 client could make any audio channel mappings required

## What is a Source, Flow, Grain, Node, Device, Sender, Receiver...?

NMOS uses these common terms (capitalised) in specific ways that may not always correspond to your expectation.  They are defined in the specifications, explained in the [Technical Overview][] and summarised in the [Glossary][].

## How can NMOS be used securely?

NMOS APIs have supported use of HTTPS and WSS (secure WebSockets) since IS-04 v1.1.

The [BCP-003][] set of Best Common Practices cover how to secure NMOS APIs:

- BCP-003-01 specifies the use of TLS 1.2 or later to encrypt communications.
  - This is now the default for accessing the IS-04 registry at AMWA workshops.
- BCP-003-02 (in progress) specifies the use of OAuth 2.0 and JSON Web Tokens to authorise API calls.

Of course, securing just the APIs isn't sufficient, and the JT-NM Tested activities now include vulnerability scanning.

## Can NMOS servers be virtualised? Can they be "in the cloud"?

Although current interest in NMOS is mostly in how to discover and connect physical devices,
 we frequently run the IS-04 Registration and Query API servers on VMs, or in Docker containers. We sometime also use public cloud hosted servers for remote testing.

## Does NMOS only deal with individual elemental Flows?

**No.** Since v1.1, IS-04 has supported multiplexed Flows. In particular, it has been used with ST 2022-6 (SDI over RTP).

Also [BCP-002-01][BCP-002] specifies how to group together "natural" groups, such as video, audio and data coming from a camera, or multiple video Receivers on a multiviewer.

## How do I make my product compatible with NMOS?

Some suggestions:

- Be familiar with the specifications, how they work, what they can and can't do.
- Read the developer resources on the Wiki.
- Try one or more [open source implementations][NMOS-Solutions].
- Use the [Testing Tool][NMOS Testing], make sure it passes.
- Join the [JT-NM Tested][] activities.
- Consider joining AMWA and participating in the Incubator and workshops, where you will benefit from a helpful community.
- Contribute issues or pull requests to GitHub.
- Ask to have you product included on the Wiki.

## How does my company / project show its Unique Selling Points?

SDI has allowed users to choose between vendors based on the functionality, features and performance of their devices (amongst other factors), and be sure that they will interoperate correctly, rather than have to think about "box X will work with box Y but not box Z".

It's similar with NMOS – but better because users will be able to use software and virtualised devices, not just hardware ones. NMOS addresses "foundational" building blocks such as identity, discovery and connection management that enable interoperability between networked devices.

But NMOS doesn't (and won't) attempt to "standardise" the functions, features, or performance of the networked devices. It will however provide a framework that manufactures can link to their specific information. For example hierarchical URIs allow allow manufacturer-specific elements to be included in JSON API messages.

## Who is AMWA?

[AMWA][] is the Advanced Media Workflow Association. Originally it was known for developing application specifications for using [MXF][] (for example the [AS-11][] family is used for delivery of finished media assets to a broadcaster or publisher).

In recent years AMWA has widened its attention to cover the application and control layers for networked media.  Much of this is happening in the Networked Media Incubator project, and this complements other industry activity looking at wire formats and codecs.

## What is the Networked Media Incubator?

AMWA set up the Networked Media Incubator (also called just "Incubator" here) in September 2015 "to establish open specifications for end-to-end identity, media transport, timing, discovery and registration, connection management and control."

## If the “O” in NMOS means “open”, why are Incubator workshops closed?

The Incubator, and its workshops, have been set up to provide an environment to encourage cooperation between members, and allow them to work together, online and at workshops towards interoperability in a low-risk way. This means that they don't have to worry about their fellow participants – who also may be their competitors – reporting negatively about their implementation, as this is forbidden by the [Incubator rules][].

In addition, there are costs associated with running AMWA and the Incubator that are met at least in part through membership fees. Companies need to be an Associate or higher member to participate, and a nominal cost Indvidual membership is also available.

Of course the NMOS specifications themselves are made publicly available (Apache 2 licence) as early as is practical, and at latest on elevation to AMWA Specification.

## Can I be sure that I won't be subject to patent fees/litigation if I implement NMOS?

AMWA's [IPR Policy][] attempts to minimise such a risk by requiring participants to disclose any knowledge of possibly relevant patents and requiring all AMWA Specifications to have an IPR review.

In addition, NMOS/Incubator is "RAND-Z" so it requires any contributions to be made available on a reasonable and non-discriminatory basis at zero cost.

And the NMOS APIs are built on widely adopted patterns used on the Internet/Web, using open-source components wherever available.

## Why does IS-04 have connection management if there is IS-05?

**History.** IS-04 v1.0 and v1.1 predate IS-05. A basic connection management mechanism was needed at earlier Incubator workshops so that we could connect together senders and receivers. This was included in IS-04 as an expediency, but doesn't really belong there.  Recent workshops use the separate, and more flexible device connection management API.  The IS-04 mechanism has been deprecated in v1.2 and may be removed in later versions.

## Why does IS-05 use SDP? And why not _just_ use SDP?

Despite its ugliness, as of 2021 [SDP][] is still regularly used with RTP media streams, and is included in SMPTE's ST 2110-10 for describing streams. So 2110-compliant senders and receivers will be using it anyway, and it makes sense for IS-05 to use it.

However, IS-05 doesn't _require_ the use of SDP. It abstracts it through the Sender's ``/transportfile`` resource, and alternative formats can be used. For a non-RTP transport this will certainly be the case (e.g. DASH manifest).

And relying on _just_ SDP itself isn't enough. It describes the streams but doesn't provide a means of making connections, so we would be reliant on proprietary mechanisms.

## Why doesn't IS-xx do yyy? How can I get yyy done?

Maybe it's because that's not something the specification should be doing. For instance, connections could use a break-before-make or make-before-break, but that is implementation/infrastructure-dependent; IS-05 is there to tell the implementation to make the connection, and possibly when to do so.

Or maybe it's a feature that is still on in development,or AMWA members have not (yet) provided a business reason for it to happen. See earlier question.

To make a difference, consider joining AMWA.

## What is the relationship between connection management and network control?

The NMOS Network Control API (IS-06) is concerned with what happens within the network itself. It deals with lower-level concepts than IS-04 and IS-05, i.e. network endpoints on NICs and switches, and control of the individual "network flows" between these.

The NMOS Connection Management API (IS-05) is concerned with creation of higher-level _logical_ connections between the Senders and Receivers of Devices. Although it's quite possible that an IS-05 connection/disconnection request may cause a controller to invoke IS-06 to "make it happen", this doesn't have to be the case.

## Can I use IS-xx without having to use IS-yy?

**Yes, but...** It's possible to use the NMOS specifications independently, but they benefit from being used in combination. For example, the Connection Management API (IS-05) allows connections between manually configured IP addresses (perhaps entered into a spreadsheet?), but this becomes unmanageable in large and changing environments, where the benefits of automated discovery using IS-04 become overwhelming.

And [TR-1001-1][] requires use of IS-05.

## How do NMOS specifications fit into the wider community activity on interoperability?

The **Joint Task Force on Networked Media** is a an industry group that was set up a few years ago to coordinate work on interoperability.  The NMOS specifications are part of the JT-NM industry roadmap and IS-04 and IS-05 are mandated in the [TR-1001-1][] recommendations.

The [EBU][EBU-R-152] and [WBU][WBU-Pyramid] support the adoption of IS-04 and IS-05.

[AMWA]: http://amwa.tv "Advanced Media Workflow Association"

[Arachnid]: https://github.com/Streampunk/arachnid "Streampunk Arachnid"

[AS-11]: http://www.amwa.tv/projects/AS-11.shtml "AMWA AS-11"

[BCP-001]: https://www.amwa.tv/specifications "AMWA BCP-001 AMWA Specification Process"

[BCP-002]: https://specs.amwa.tv/bcp-002 "AMWA BCP-002 Recommendations for Grouping NMOS Resources"

[BCP-003]: https://specs.amwa.tv/bcp-003 "AMWA BCP-003 Security recommendations for NMOS APIs"

[EBU-R-152]: https://tech.ebu.ch/publications/r152 "EBU R 152: Strategy for the Adoption of an NMOS Open Discovery & Connection Protocol"

[EBU-Tech-3371]: https://tech.ebu.ch/publications/tech3371 "EBU Tech 3371: The Technology Pyramid for Media Nodes: Minimum User Requirements to build and manage an IP-Based Media Facility"

[Glossary]: https://specs.amwa.tv/nmos/branches/main/docs/Glossary.html "Glossary"

[NMOS-Solutions]: https://specs.amwa.tv/nmos/branches/master/NMOS-Solutions.html "NMOS Solutions"

[Incubator rules]: https://www.amwa.tv/bylaws-policy-documents-and-license "AMWA Networked Media Incubator rules"

[IPR Policy]: https://www.amwa.tv/bylaws-policy-documents-and-license "AMWA IPR Policy"

[JT-NM]: http://jt-nm.org "Joint Task Force on Networked Media (JT-NM)"

[JT-NM Reference Architecture]: http://jt-nm.org/RA-1.0/ "JT-NM Reference Architecture 1.0"

[JT-NM Tested]: http://jt-nm.org/jt-nm_tested/ "JT-NM Tested"

[MXF]: http://tech.ebu.ch/docs/techreview/trev_2010-Q3_MXF-2.pdf "MXF - a technical review"

[NMOS Documentation]: https://specs.amwa.tv/nmos "NMOS Documentation"

[NMOS Testing]: https://amwa-tv.github.io/nmos-testing/ "NMOS API Testing Tool"

[Parameter Registers]: https://specs.amwa.tv/nmos-parameter-registers/ "NMOS Parameter Registers"

[Scalability]: http://www.ipshowcase.org/wp-content/uploads/2019/05/1030-Robert-Porter-Scalability-and-Performance-of-IS-04-and-IS-05-and-How-TR-1001-1-Helps.pdf "Scalability"

[SDP]: https://tools.ietf.org/html/rfc4566 "SDP: Session Description Protocol"

[ST 2110]: https://www.smpte.org/smpte-st-2110-faq "ST 2110 FAQ"

[Technical Overview]: https://specs.amwa.tv/nmos/branches/master/NMOS_Technical_Overview.html "NMOS Technical Overview"

[TR-1001-1]: http://www.jt-nm.org/documents/JT-NM_TR-1001-1:2018_v1.0.pdf "JT-NM TR-1001: System Environment and Device Behaviors For SMPTE ST 2110 Media Nodes in Engineered Networks"

[Upgrade Path]: https://specs.amwa.tv/is-04/v1.3/docs/6.0._Upgrade_Path.html "Upgrade Path"

[WBU-Pyramid]: https://worldbroadcastingunions.org/wbu-supports-ebus-technology-pyramid-for-media-nodes/ "WBU Endorses EBU Pyramid"

[NMOS Wiki]: https://github.com/AMWA-TV/nmos/wiki "NMOS Wiki"
