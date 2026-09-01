WEEK 1 - DAY 1: HTTP/HTTPS FUNDAMENTALS AND PASSIVE RECONNAISSANCE

Date: August 28, 2026
Program Phase: Phase 1, Foundations
Module Focus: Web Protocol Fundamentals and HTTP Response Header Analysis
Analyst: Evans (Evxns-OS)


EXECUTIVE SUMMARY

This report documents the first day of a structured cybersecurity training program, covering the theoretical foundation of web request architecture and a practical passive reconnaissance exercise conducted against a live, public web application. All activity was strictly observational and non-intrusive, consistent with the ethical and legal boundaries defined for this program.


1. OBJECTIVE

To understand the complete lifecycle of a web request, from domain resolution through page rendering, and to develop practical reconnaissance skills through the analysis of HTTP response headers returned by a live web application.


2. THEORETICAL FOUNDATION: THE ANATOMY OF A WEB REQUEST

Before any content is exchanged between a client and a server, four distinct layers operate in sequence to establish and secure the connection.

Step 1, DNS: Resolves the human-readable domain name into a numeric IP address.
Step 2, TCP: Establishes a reliable connection via a three-way handshake (SYN, SYN-ACK, ACK).
Step 3, TLS: Encrypts the connection; present only when the site is served over HTTPS.
Step 4, HTTP: The actual request and response exchange occurs over the now-secured connection.

Key clarification: HTTPS is not a distinct protocol from HTTP. It is the standard HTTP protocol operating within a TLS-encrypted tunnel. DNS resolution, TCP connection establishment, and the TLS handshake all occur prior to the HTTP exchange itself.


3. METHODOLOGY

Target: car.com, a public website, examined for passive observation purposes only (see Section 8, Ethics and Legality Statement).

Technique: Standard browser page load with response headers inspected via Firefox Developer Tools, Network tab, executed within a Kali Linux virtual machine. A hard reload (Ctrl+Shift+R) was performed to bypass cached 304 Not Modified responses and ensure fresh 200 OK responses were captured for analysis.

Environment: Kali Linux virtual machine, Firefox browser, Burp Suite Community Edition installed and configured. Proxy interception was not utilized for this particular exercise, reserved for later modules.


4. FINDINGS

4.1 Primary Document Analysis

Request: / (car.com homepage)

Identification criteria: This request appears first in the Network tab request list, contains no file extension in its Request URL, and is classified under Type document or html, distinguishing it from subsequent static asset requests.

[Insert screenshot: images/day1-html-headers.png]

content-type: text/html; charset=utf-8
Confirms this is the primary HTML document, UTF-8 encoded.

content-length: 199455
Response body size, approximately 195 KB.

cache-control: public, max-age=31536000, s-maxage=31536000
Cached for one year across both browser and shared (CDN) caches. Notably extended for HTML content and flagged for further review in subsequent modules.

etag: "ava2bdoyi049s0"
Strong entity tag requiring exact byte match, used for change detection without full re-download.

request-context: appId=cid-v1:
Internal Azure telemetry identifier, not security-relevant.

x-nextjs-cache: HIT
Confirms an application-level Next.js caching layer, distinct from CDN caching, served this response from cache.

x-powered-by: Next.js
Direct confirmation of the application framework in use.

x-azure-ref: present, unique per request
Confirms hosting infrastructure: Microsoft Azure.

x-cdn: FrontDoor
Confirms CDN in use: Azure Front Door.

x-generated-by: AutoWeb
Indicates an internal build tool or platform.

is_mobile / is_tablet: false / false
Server-side device detection results for this request.

rule-isdesktop: IsDesktop
Internal rule confirming desktop as the detected device category.

x-cache: CONFIG_NOCACHE
Despite the extended cache-control directive, this specific response was not cached at the CDN edge layer.

accept-ranges: bytes
Indicates support for partial content requests.


4.2 Static Asset Analysis

Request: /_next/static/chunks/7627-2ac8d4126664553d.js

Identification criteria: This request appears after the primary document in the Network tab, carries a .js extension in its Request URL, and is classified under Type js or javascript. The browser requests this only after parsing the HTML and encountering the corresponding script reference.

[Insert screenshot: images/day1-js-headers.png]

content-type: application/javascript; charset=UTF-8
Confirms asset type.

cache-control: public, max-age=31536000, s-maxage=31536000
Cached for one year, considered safe due to the content-hashed filename, whereby any change to file content produces a new filename, eliminating the risk of stale content being served.

etag: W/"473d-19edbacabb8"
Weak entity tag, applying a looser comparison than a strong tag, appropriate for static assets.

x-azure-ref: present
Confirms hosting infrastructure: Microsoft Azure.

x-cdn: FrontDoor
Confirms CDN in use: Azure Front Door.

x-cache: CONFIG_NOCACHE
This specific asset was not cached at the CDN edge layer for this request.


5. RECONNAISSANCE FINDINGS SUMMARY

Application Framework: Next.js (React-based)
Cloud Infrastructure Provider: Microsoft Azure
CDN / Edge Service: Azure Front Door
Internal Build Tooling: AutoWeb (name observed via header, specific purpose unconfirmed)


6. COMPARATIVE ANALYSIS: PRIMARY DOCUMENT VS. STATIC ASSET

Examining both request types in tandem, rather than in isolation, produced a more complete and reliable infrastructure profile than either would independently. The primary document's headers directly identified the application framework (x-powered-by) and revealed an application-level caching mechanism (x-nextjs-cache) operating independently of CDN-level caching. The static asset's headers demonstrated a distinct caching strategy, hash-based cache invalidation, and employed a weaker entity tag appropriate for infrequently modified content. Both requests independently corroborated the same hosting provider and CDN, cross-validating the findings rather than relying on a single data point.


7. VULNERABILITIES IDENTIFIED

None. This session was scoped exclusively to passive reconnaissance techniques, in accordance with the Phase 1 objectives of this program. No active testing, scanning, or exploitation was performed, and no vulnerability assessment was conducted against car.com or any other live system. Vulnerability identification and exploitation activities commence in Week 4 of this program and will be performed exclusively against authorized, purpose-built lab environments, never against live third-party systems such as car.com.


8. ETHICS AND LEGALITY STATEMENT

All activity documented in this report constitutes passive reconnaissance only, standard, unmodified page requests equivalent to normal visitor browsing behavior. No active scanning, enumeration, brute-forcing, or intrusive testing was performed against car.com or any third-party system.

In accordance with the ethical guidelines of this program, all active testing techniques introduced in subsequent modules, including port scanning, directory brute-forcing, vulnerability scanning, and exploitation, will be performed exclusively against authorized, purpose-built lab environments: DVWA, OWASP Juice Shop, Metasploitable2, TryHackMe, and HackTheBox.


9. KEY TAKEAWAY

A significant volume of infrastructure information can be gathered through entirely passive means, simply by observing the headers returned during normal page navigation. This underscores why response header hygiene, such as minimizing unnecessary disclosure through headers like x-powered-by, is itself a recognized security best practice, and why header analysis typically constitutes the first step in any professional reconnaissance methodology.


10. STATUS

Lab environment setup complete (Kali Linux, Burp Suite) - Done
Public GitHub documentation repository established - Done
Day 1 objectives met - Done
Proceeding to Day 2, DNS and the Network Layer - Pending


Documented as part of a 20-week Cybersecurity and Web Security Analyst training program.