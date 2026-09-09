# Abstract

This paper analyzes the application-layer communication used by *Dragon Ball: The Breakers* between the Steam client and Bandai Namco's Cosmos Channel backend services. Based on captured HTTP/2 traffic, the analysis identifies HTTPS transport, MessagePack-encoded application data, and two backend services: a common User/System API and a game-specific API.

The observed requests share a common structure containing the title code, user identifier, session, platform, and, for game-specific requests, a version field. Responses contain result metadata and an evolving session value. Several endpoints were reconstructed, including authentication, country lookup, tracking-number retrieval, user-information processing, maintenance information, and adjustment-data retrieval.

The analysis establishes the observed protocol structure while identifying several endpoint-specific fields and the large adjustment-data payload as areas requiring further investigation.

---

[Back to document map](README.md)
