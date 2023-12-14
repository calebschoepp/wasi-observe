# WASI Observe World

A proposed [WebAssembly System Interface](https://github.com/WebAssembly/WASI) API.

### Current Phase

wasi-observe is currently in [Phase 1].

[Phase 1]: https://github.com/WebAssembly/WASI/blob/42fe2a3ca159011b23099c3d10b5b1d9aff2140e/docs/Proposals.md#phase-1---feature-proposal-cg

### Champions

- Chris Dickinson ([Dylibso][dylibso])

[dylibso]: https://dylibso.com/

### Portability Criteria

TODO before entering Phase 2.

## Table of Contents [if the explainer is longer than one printed page]

- [Introduction](#introduction)
- [Goals [or Motivating Use Cases, or Scenarios]](#goals-or-motivating-use-cases-or-scenarios)
- [Non-goals](#non-goals)
- [API walk-through](#api-walk-through)
  - [Use case 1](#use-case-1)
  - [Use case 2](#use-case-2)
- [Detailed design discussion](#detailed-design-discussion)
  - [[Tricky design choice 1]](#tricky-design-choice-1)
  - [[Tricky design choice 2]](#tricky-design-choice-2)
- [Considered alternatives](#considered-alternatives)
  - [[Alternative 1]](#alternative-1)
  - [[Alternative 2]](#alternative-2)
- [Stakeholder Interest & Feedback](#stakeholder-interest--feedback)
- [References & acknowledgements](#references--acknowledgements)

### Introduction

WASI Observe exposes observability ("o11y") tracing, logging, and metrics
interfaces to Wasm components. The primary motivation of the interface is to
allow for manual and automatic instrumentation of components that consume the
interface (**"guests"**), while allowing components instances that implement
the interface (**"hosts"**) sufficient context to ship the produced data to an
o11y **vendor** (DataDog, Honeycomb.) The protocol used by the host to
communicate with the vendor is an implementation detail of the providing
component.

> TKTK: "vendor" might be the wrong word there, should that be "collector"?

#### An aside about the origin of the proposal

Dylibso extracted the interface proposal [from an effort][dylibso-pr] to port
Dylibso's Observe-SDK bindings, which target Wasm Core Modules, to the Wasm
Component Model; the initial proposal reflects those origins. In particular:

- The host is expected to track and maintain span state for guests.
- The interface is isomorphic across components and core modules. A runtime
  implementing the host interface only needs to implement the interface once.

These properties are not set in stone!

[dylibso-pr]: https://github.com/dylibso/observe-sdk/pull/128

### Goals

#### Technical Values

- WASI Observe is a pipe. The interesting O11Y work happens at either end of
  this pipe: at guest instrumentation or host collection.
- Using WASI Observe should be as transparent as possible for authors of guest
  applications.
- TKTK(proposed value)?: Target the OTel specification as the "base layer" of functionality.

---

#### A Programmer is Required to Implement O11Y Instrumentation

A programmer working at a company goes to implement a new service. The Site
Reliability Engineering function of the engineering department requires that
new services be instrumented using standard tooling. The programmer installs an
instrumentation package using the appropriate package manager (`npm`, `cargo`,
`curl -o`), configures the package according to the documentation, and pushes
the code.

The instrumentation Just Works (TM.) In this scenario, it is not necessary for
the programmer to be aware that their service is compiled to a Wasm component,
or to make any adjustments to accommodate the compile target.

#### An O11Y Vendor Evaluates Supporting Wasm Components

A hypothetical O11Y Vendor, Omni Consumer Products ("OCP"), evaluates
supporting Wasm Components as a target. OCP maintains instrumentation libraries
across several language platforms: JavaScript, Ruby, Golang, Rust, Python, and
others; collocated collector services meant to be run by their customers;
short- and long-term data storage; and visualization dashboards.

OCP takes particular care to ensure that on-site collector services and
instrumentation libraries introduce as little resource overhead as possible,
both in terms of CPU time and memory use.

In order to support Wasm Components across their language platform offerings,
OCP are able to introduce low-cost checks for the availability of WASI Observe
functions at initialization time. Where the Observe functions are available
they can be used to intercept incoming domain objects (spans, logs, metrics)
with minimal modification to the incoming data.

Looking forward, OCP is considering compiling their on-site collector service
to a Wasm component to more directly intercept and export o11y data off-site
using fewer resources with a better security profile.

Buoyed by the success of this approach, OCP is considering automatic
instrumentation of components at component instantiation points.

#### A Site-Reliability Engineer Considers Supporting Wasm Components

A Site Reliability Engineering engineer ("SRE") working at a large software as
a service company evaluates whether supporting Wasm components is feasible at
this time. The SRE is responsible for the operation and maintenance of several
production services. Before adopting a new platform target, the SRE considers
the telemetry that platform makes available.

Having considered the telemetry available through the WASI observe tooling, the
SRE determines that the Wasm component model is an acceptable platform target.

### Non-goals

- TKTK

### API walk-through

The full API documentation can be found [here](imports.md).

### Stakeholder Interest & Feedback

TODO before entering Phase 3.

[This should include a list of implementers who have expressed interest in implementing the proposal]

### References & acknowledgements

Many thanks for valuable feedback and advice from:

- [Person 1]
- [Person 2]
- [etc.]
