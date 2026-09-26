# Security Policy

## Supported versions

Security fixes are provided for the latest published version of LMU Pit Companion. Users may be asked to update before a report is investigated.

## Reporting a vulnerability

Do not disclose a suspected vulnerability in a public GitHub issue.

Contact the maintainer privately at `YOUR_SECURITY_CONTACT` and include:

- the affected version;
- a concise description of the issue and its impact;
- reproduction steps or a proof of concept;
- any suggested mitigation;
- whether the issue has been disclosed elsewhere.

Please avoid accessing data that is not yours, disrupting services, or publishing exploit details before a fix is available.

## Relevant security boundaries

LMU Pit Companion reads telemetry exposed by SimHub and communicates with LMU's local Pit Menu API on the same computer. It does not require inbound network access and the public release does not include the separate cloud-sync project.
