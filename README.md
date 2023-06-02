# TACOS-spec
TACOS Framework v1

## Overview
TACOS is a framework for assessing the development practices of open source projects against a set of secure development standards specified by the [NIST Secure Software Delivery Framework (SSDF) V1.1](https://csrc.nist.gov/Projects/ssdf).

## Attestation
The attestation itself is metadata that contains the history, timestamp and identity of who is making the statements, in addition to the statements themselves.

## Statements
At the heart of TACOS are statements about open source software development practices at the package level (not per-release at this time). It leverages a model of standards and assessment to provide a piece of metadata in response to that standard. The states of assessment against each objective standard are: True, False, and `NOASSERTION`. In some cases, such as licensing or SDLC Policy URL, it will be useful to include the direct piece of metadata or the evidence URL itself.  

The end product is a machine-readable format that can be referenced in an SBOM, or provided as a standalone report as a complementary artifact to software vendor attestation on their own internal software development practices.

Each attestation has a timestamp associated with it, and assessments and resulting statements are refreshed according to these events:
- A new release of a package has been made
- A new release of one of a package’s dependencies has been made
- A new vulnerability is logged
- Periodic, timestamped cadence for manually validated data, such as verified income streams and [early signal that a package may be on a path to being abandoned](https://blog.tidelift.com/up-to-20-percent-of-your-application-dependencies-may-be-unmaintained)

### Attestation statement field structure and assessment criteria (all fields are required except as noted)
|Field   |Possible Values   |Description   |Assessment   |
|:-|:--|:--|:--|
|PackageName   |String   |Canonicalize package name   |Canonicalization process applied to metadata   |
|PackagePlatform   |String   |Canonicalize package manager for the package   |Canonicalization process applied to metadata   |
|PURL   |[PURL](https://github.com/package-url/purl-spec#some-purl-examples)   |Validated PURL (package URL) for the package   |Canonicalization process applied to metadata   |
|UpstreamRepositoryURL   |URL   |Validated URL for source code in order to do other standards assessments, and record changes in its dependencies   |Manually verified upstream repository url metadata   |
|SPDXLicenseLatestRelease   |SPDX expression   |Normalized, machine readable licensing data   |Manually verified, SPDX formatted metadata   |
|LatestStableRelease   |String   |Point release, mapped in the correct versioning scheme   |Manually verified versioning scheme and ingestion of latest stable (no alpha, RC, etc.) release from upstream package managers resulting in metadata   |
|ReleasesInUse **(not required)**   |Array of Strings   |Releases in use within an application or organization (not required)   |Metadata derived from SBOM output, and dependency graph mapping to attest to full graphs   |
|SBOM   |Array of Objects   |CycloneDX or SPDX SBOM of build of latest release. Each SBOM field has the following subfields   |Ability to generate and deliver the SBOM   |
|SBOM.[]Type   |String   |One of “CycloneDX” or “SPDX”   |   |
|SBOM.[]URL   |URL   |URL of the referenced SBOM   |   |
|SBOM.[]DigitalSignatureURL   |URL   |URL for the digital signature of the SBOM referred to by SBOM.URL   |   |
|PackageManager2FAEnabled   |"True"\|"False"\|"NOASSERTION"   |First-party attestation to 2FA practices on the release processes   |Maintainers use 2FA to push releases to the package manager   |
|SourceRepo2FAEnabled   |"True"\|"False"\|"NOASSERTION"    |First-part attestation to 2FA practices on the source repository   |Maintainers use 2FA to access the source code repository   |
|KnownReleasesURL   |URL   |URL that returns a mapping of all releases for this package   |Mapped metadata generated off of verified versioning scheme   |
|CleanReleaseAvailable   |"True"\|"False"\|"NOASSERTION"    |There is a release available of this package that is free from vulnerabilities, and the latest compatible version of all dependencies are maintained and have no known vulnerabilities   |Release available with no vulnerabilities in package or package dependencies, and no unmaintained dependencies   |
|NoKnownVulnerabilities   |"True"\|"False"\|"NOASSERTION"    |This package's latest release includes fixes for all known vulnerabilities |There are zero CVEs for the package on the latest release   |
|NoKnownVulnerabilitiesInDependencies   |"True"\|"False"\|"NOASSERTION"    |The dependencies of this package's latest release all have no known vulnerabilities in their latest releases |There are zero CVEs in the latest release of all dependencies of the latest package release   |
|KnownVulnerabilitiesURL   |URL   |URL listing all vulnerabilities that affect the package and its dependencies   |Mapped metadata, including maintainer verification on which release streams are receiving security updates   |
|PackageSecurityPolicyURL   |URL   |URL of active security policy with clear steps for coordinated disclosure of vulnerabilities   |Discoverable security policy exists   |
|PackageSecurityContact   |URL   |URL of active security contact in the event of a vulnerability   |Discoverable security contact exists   |
|NoBinariesInRepository   |"True"\|"False"\|"NOASSERTION"    |Identifies whether the project has generated executable (binary) artifacts in the source repository that could result in maliciously subverted executables   |Passes OpenSSF Scorecard binary artifacts check OR Maintainer attestation to override automated check   |
|CodeReviewPractice   |"True"\|"False"\|"NOASSERTION"    |Consistent review of PRs in order to address bugs or vulnerabilities sooner, and also any potential contributions from bad actors   |Passes OpenSSF Scorecard code review check OR Maintainer attestation to override automated check   |
|FuzzingPractice   |"True"\|"False"\|"NOASSERTION"    |Assessing the practice of feeding random data in to expose exploitable bugs   |Passes OpenSSF Scorecard fuzzing check OR Maintainer attests to use of a fuzzing tool   |
|ReproducibleBuilds   |"True"\|"False"\|"NOASSERTION"    |Hashes from source code repo and package manager can be verified   |Maintainer attestation   |
|ReleasesDigitallySigned   |"True"\|"False"\|"NOASSERTION"    |Attests to the provenance of artifacts   |Maintainer attestation or manually verified metadata   |
|SDLCPolicyURL   |URL   |URL of development practices or standards for the package  |Discoverable body of SDLC standards exists   |
|SDLCEvidenceDataURL   |URL   |URL of package development and development practices activity   |Discoverable record of SDLC actions and maintainer attestations exists   |
|PackageStatus   |Object    |Validated data on the package that may be signaling that it will be abandoned, deprecated, or moved to EOL   |Passes automated checks and manual verification that indicates there is at least one maintainer that is paying attention to potential bugs or security risks that could compromise the software supply chain   |
|PackageStatus.Status   |String   |One of "Active", "Inactive", "Deprecated", "Removed", or "EOL"   |   |
|PackageStatus.LastModifiedDateTimeUTC   |Timestamp   |Logged time of manual validation in UTC   |   |
|IncomeStreams   |Array of Objectsj|"NOASSERTION"    |Package has validated income streams including lifting income from Tidelift, corporate backing, foundation, or variable/community backing   |Manually verified OR maintainer attested metadata   |
|IncomeStreams.[]Type   |String   |Any of "Lifted", "Corporate", "Foundation", "Variable", or "None"   |   |
|IncomeStreams.[]EvidenceURL   |URL   |URL of the income stream evidence   |   |
|IncomeStreams.[]LastModifiedDateTimeUTC   |Timestamp   |Logged time of manual validation   |   |


### NIST SSDF mappings
Each field in this framework maps to the [NIST Secure Software Development Framework (SSDF) v1.1](https://csrc.nist.gov/Projects/ssdf). This framework is a continuum of software practices from setting a solid foundation for an organization and clear processes and standards for success, through the actual development work, and to the state where ‘what happens when problems arise’. 

The NIST SSDF v. 1.1 categories are:
 1. **Prepare the organization:** Organizations should ensure that people, processes, and technology are prepared to perform secure software development at the organization level.
- IncomeStreams
- PackageManager2FAEnabled
- SourceRepo2FAEnabled
- LatestStableRelease
- KnownReleasesURL
- PackageStatus
- SDLCPolicyURL
- SDLCEvidenceDataURL

2. **Protecting the software:** Organizations should protect all components of their software from tampering and unauthorized access. 
- SBOM: Type, Version, URL, Format, Digital signature
- NoBinariesInRepository
- FuzzingPractice
- ReproducibleBuilds
- ReleasesDigitallySigned

3. **Produce well-secured software:** Organizations should leverage people, processes and tools to produce well-secured software with minimal security vulnerabilities in its releases.
- PackageName
- UpstreamRepositoryURL
- CleanReleaseAvailable
- CodeReviewPractice
- PackageSecurityPolicyURL
- PackageSecurityContact

4. **Respond to vulnerabilities:** Organizations should identify residual vulnerabilities in their software releases and have process and accountability to respond appropriately to those vulnerabilities and prevent similar ones from occurring in the future.
- CleanReleaseAvailable
- NoKnownVulnerabilities
- NoKnownVulnerabilitiesInDependencies
- KnownVulnerabilitiesURL
