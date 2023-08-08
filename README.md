# TACOS-spec
TACOS Framework v1

## Overview
TACOS is a framework for attesting to the development practices of open source projects, in order to help organizations assess and attest to such development practices. This assessment and attestation can be useful both internally, and to help comply with regulations that mandate adherence to specific secure software development practices.

The end product of a TACOS assessment is a machine-readable artifact that can be referenced in a Software Bill of Materials (SBOM) external reference field, provided as a standalone report, or as a complementary artifact alongside other software development practice analyses.

## TACOS statements
At the heart of TACOS are statements about the open source software development practices of an upstream open source software package. These statements are generally made at the package level, with statements about specific versions possible in a given attestation report (see below). 

There are two types of statements. Attestation statements attest to adherence to a particular secure development standard. Each attestation statement must have a value of:  True, False, or `NOASSERTION`. Metadata statements describe a particular attribute of the package, or reference a document with further information (such as a URL or SBOM). 

## Assessments
The specification defines what assessment should be performed for any TACOS statement that is associated with an assessment. Assessment definitions may include references to third-party assessment standards. If such a reference exists, assessors should familiarize themselves with the third-party standard in order to inform their assessment. 

## Attestation reports
A TACOS attestation report is a collection of TACOS statements. Each report must have:
  - the creation date in ISO 8601 format (see, eg, https://spdx.github.io/spdx-spec/v2.3/document-creation-information/#69-created-field)
  - an indication of the identity of the report’s creator (see, eg, https://spdx.github.io/spdx-spec/v2.3/document-creation-information/#68-creator-field)

In accordance with U.S. government standards, new attestation reports for a software product should be generated whenever a new major release is made [src](https://www.cisa.gov/sites/default/files/2023-04/secure-software-self-attestation_common-form_508.pdf).

At this time, we have not formally specified the structure of an attestation report.

### Attestation statement field structure and assessment criteria (all fields are required except as noted)
|Field   |Type   |Possible Values   |Description   |Assessment   |
|:-|:-|:-|:--|:--|
|PackageName   |Metadata   |String   |Canonicalized package name   |N/A   |
|PackagePlatform   |Metadata   |String   |Canonicalized package manager for the package   |N/A   |
|PURL   |Metadata   |[PURL](https://github.com/package-url/purl-spec#some-purl-examples)   |PURL (package URL) for the package   |N/A   |
|UpstreamRepositoryURL   |Metadata   |URL   |Validated URL for source code in order to do other standards assessments, and record changes in its dependencies   |An upstream source code repository exists and is publicly referenceable by a URL   |
|SPDXLicenseLatestRelease   |Attestation   |SPDX expression   |Normalized, machine readable licensing data   |Manually verified, SPDX formatted metadata   |
|LatestStableRelease   |Metadata   |String   |Point release, mapped in the correct versioning scheme   |Manually verified versioning scheme and ingestion of latest stable (no alpha, RC, etc.) release from upstream package managers resulting in metadata   |
|ReleasesInUse **(not required)**   |Metadata   |Array of Strings   |Releases in use within an application or organization (not required)   |Metadata derived from SBOM output, and dependency graph mapping to attest to full graphs   |
|SBOM   |Metadata   |Array of Objects   |CycloneDX or SPDX SBOM of build of **latest release**. Each SBOM field has the following subfields   |Ability to generate and deliver the SBOM   |
|SBOM.[]Type   |Metadata   |String   |One of “CycloneDX” or “SPDX”   |   |
|SBOM.[]URL   |Metadata   |URL   |URL of the referenced SBOM   |   |
|SBOM.[]DigitalSignatureURL   |Metadata   |URL   |URL for the digital signature of the SBOM referred to by SBOM.URL   |   |
|PackageManagerMFAEnabled   |Attestation   |"True"\|"False"\|"NOASSERTION"   |Maintainer attests to using multifactor authentication when accessing the build and release environment for software   |Maintainer has attested to using 2FA on the package manager    |
|SourceRepoMFAEnabled   |Attestation   |"True"\|"False"\|"NOASSERTION"    |Maintainer attests to using multifactor authentication when accessing the development environment for software   |Maintainer has attested to using 2FA on  the upstream source code repository   |
|DocumentedBuildPractices   |Attestation   |"True"\|"False"\|"NOASSERTION"    |Maintainer attests to build practices while developing the software   |Maintainer has attested to current build practices   |
|KnownReleasesURL   |Metadata   |URL   |URL that returns a mapping of all releases for this package   |Mapped metadata generated off of verified versioning scheme   |
|CleanReleaseAvailable   |Attestation   |"True"\|"False"\|"NOASSERTION"    |There is a release available of this package that is free from vulnerabilities, and the **latest compatible version of all dependencies** and have no known vulnerabilities   |Release available with no vulnerabilities in package or package dependencies   |
|FixedVulnerabilities   |Attestation   |"True"\|"False"\|"NOASSERTION"    |This package's latest release includes fixes for all known vulnerabilities |There are no unfixed or unreviewed CVEs for the package on the latest release   |
|DependenciesAreManaged   |Attestation   |"True"\|"False"\|"NOASSERTION"    |The dependencies of this package's latest release all have no known vulnerabilities in their latest releases and are not deprecated |Using the latest package release will not bring in transitive vulnerabilities or deprecated packages   |
|KnownVulnerabilitiesURL   |Metadata   |URL   |URL listing all vulnerabilities that affect the package and its dependencies   |Mapped metadata, including maintainer verification on which release streams are receiving security updates   |
|PackageSecurityPolicyURL   |Metadata   |URL   |URL of active security policy with clear steps for coordinated disclosure of vulnerabilities   |Discoverable security policy exists   |
|PackageSecurityContact   |Metadata   |URL   |URL of active security contact in the event of a vulnerability   |Discoverable security contact exists   |
|SecurityResponsive   |Attestation   |"True"\|"False"\|"NOASSERTION"    |Identifies whether the project is likely to respond to security issues   |Passes automated checks and manual verification that indicates there is at least one maintainer that is paying attention to potential bugs or security risks that could compromise the software supply chain   |
|MultipleMaintainers   |Attestation   |"True"\|"False"\|"NOASSERTION"    |Identifies whether the project has multiple maintainers to ensure package and community sustainability   |>1 release manager detected through package manager   |
|SuccessionPlan   |Attestation   |"True"\|"False"\|"NOASSERTION"    |Identifies whether the project has a succession plan in place when a maintainer is ready to stop maintaining the package   |Maintainer attestation exists   |
|NoBinariesInRepository   |Metadata   |"True"\|"False"\|"NOASSERTION"    |Identifies whether the project has generated executable (binary) artifacts in the source repository that could result in maliciously subverted executables   |Passes OpenSSF Scorecard binary artifacts check   |
|CodeReviewPractice   |Metadata   |"True"\|"False"\|"NOASSERTION"    |Consistent review of PRs in order to address bugs or vulnerabilities sooner, and also any potential contributions from bad actors   |Passes OpenSSF Scorecard code review check   |
|FuzzingPractice   |Metadata   |"True"\|"False"\|"NOASSERTION"    |Assessing the practice of feeding random data in to expose exploitable bugs   |Passes OpenSSF Scorecard fuzzing check   |
|ReleasesDigitallySigned   |Metadata   |"True"\|"False"\|"NOASSERTION"    |Attests to the provenance of artifacts   |Passes OpenSSF Scorecard signed releases check   |
|SDLCPolicyURL   |Metadata   |URL   |URL of development practices or standards for the package  |Discoverable body of SDLC standards exists   |
|SDLCEvidenceDataURL   |Metadata   |URL   |URL of package development and development practices activity   |Discoverable record of SDLC actions and maintainer attestations exists   |
|PackageStatus   |Attestation   |Object    |Validated data on the package that may be signaling that it will be abandoned, deprecated, or moved to EOL   |Passes automated checks and manual verification that indicates there is at least one maintainer that is paying attention to potential bugs or security risks that could compromise the software supply chain   |
|PackageStatus.Status   |String   |One of "Active", "Inactive", "Deprecated", "Removed", or "EOL"   |   |
|PackageStatus.LastModifiedDateTimeUTC   |Timestamp   |Logged time of manual validation in UTC   |   |
|IncomeStreams   |Metadata   |Array of Objects\|"NOASSERTION"    |Package has validated income streams including lifting income from Tidelift, corporate backing, foundation, or variable/community backing   |Manually verified OR maintainer attested metadata   |
|IncomeStreams.[]Type   |Metadata   |String   |Any of "Lifted", "Corporate", "Foundation", "Variable", or "None"   |   |
|IncomeStreams.[]EvidenceURL   |Metadata   |URL   |URL of the income stream evidence   |   |
|IncomeStreams.[]LastModifiedDateTimeUTC   |Metadata   |Timestamp   |Logged time of manual validation   |   |


## Status and goals
We intend to continue [revising this framework](https://github.com/tacosframework/roadmap) based on customer feedback, community feedback, security research, and government regulation. As part of that, we will formalize the specification and seek adoption by relevant software supply chain security standards body groups.

### CISA attestation form mappings
|Attestation Requirements   |Related EO 14028 Subsection   |Related SSDF Practices and Tasks    |TACOS framework   |
|:--|:-|:-|:--|
|1) The software was developed and built in secure environments. Those environments were secured by the following actions, at a minimum:   |4e(i)   |[See rows below]   |[See rows below]   |
|a) Separating and protecting each environment involved in developing and building software;   |4e(i)(A)   |PO.5.1   |DocumentedBuildPractices  |
|b) Regularly logging, monitoring, and auditing trust relationships used for authorization and access: <br />i) to any software development and build environments; and <br />ii) among components within each environment;    |4e(i)(B)   |PO.5.1   |DocumentedBuildPractices<br />NoBinariesInRepository<br />CodeReviewPractice  |
|c) Enforcing multi-factor authentication and conditional access across the environments relevant to developing and building software in a manner that minimizes security risk;    |4e(i)(D)   |PO.5.1   |DocumentedBuildPractices<br />PackageManagerMFAEnabled<br />SourceRepoMFAEnabled  |
|d) Taking consistent and reasonable steps to document, as well as minimize use or inclusion of software products that create undue risk, within the environments used to develop and build software;     |4e(i)(D)   |PO.5.1, PO.5.2   |SBOM: Type, Version, URL, Format, Digital signature<br />CodeReviewPractice  |
|e) Encrypting sensitive data, such as credentials, to the extent practicable and based on risk;     |4e(i)(E)    |PO.5.2   |DocumentedBuildPractices  |
|f) Implementing defensive cyber security practices, including continuous monitoring of operations and alerts and, as necessary, responding to suspected and confirmed cyber incidents;      |4e(i)(F)    |PO.3.2, PO.3.3, PO.5.1, PO.5.2   |SecurityResponsive<br />IncomeStreams  |
|2) The software producer has made a good-faith effort to maintain trusted source code supply chains by: <br />a) Employing automated tools or comparable processes; and <br />b) Establishing a process that includes reasonable steps to address the security of third-party components and manage related vulnerabilities;      |4e(iii)    |PO 1.1, PO.3.1, PO.3.2, PO.5.1, PO.5.2, PS.1.1, PS.2.1, PS.3.1, PW.4.1, PW.4.4, PW 7.1, PW 8.1, RV 1.1   |SDLCPolicyURL<br />SDLCEvidenceDataURL<br />CleanReleaseAvailable<br />DependenciesAreManaged<br />FixedVulnerabilities<br />DependenciesAreManaged<br />FuzzingPractice<br />IncomeStreams  |
|3) The software producer maintains provenance data for internal and third-party code incorporated into the software;       |4e(vi)    |PO.1.3, PO.3.2, PO.5.1, PO.5.2, PS.3.1, PS.3.2, PW.4.1, PW.4.4, RV.1.1, RV.1.2   |SBOM: Type, Version, URL, Format, Digital signature<br />ReleasesDigitallySigned  |
|4) The software producer employed automated tools or comparable processes that check for security vulnerabilities. In addition:<br />a) The software producer ensured these processes operate on an ongoing basis and, at a minimum, prior to product, version, or update releases and <br />b) The software producer has a policy or process to address discovered security vulnerabilities prior to product release; and <br />c) The software producer operates a vulnerability disclosure program and accepts, reviews, and addresses disclosed software vulnerabilities in a timely fashion.       |4e(iv)    |PO.4.1, PO.4.2, PS.1.1, PW.2.1, PW.4.4, PW.5.1, PW.6.1, PW.6.2, PW.7.1, PW.7.2, PW.8.2, PW.9.1, PW.9.2, RV.1.1, RV.1.2, RV.1.3, RV.2.1, RV.2.2, RV.3.3    |SDLCPolicyURL<br />PackageSecurityPolicyURL<br />PackageSecurityContact<br />SecurityResponsive<br />CleanReleaseAvailable<br />SDLCEvidenceDataURL<br />IncomeStreams  |


### NIST SSDF mappings
Each attestation in this framework maps to the [NIST Secure Software Development Framework (SSDF) v1.1](https://csrc.nist.gov/Projects/ssdf) as well as the published draft of [CISA’s attestation form](https://www.cisa.gov/sites/default/files/2023-04/secure-software-self-attestation_common-form_508.pdf). This framework is a continuum of software practices from setting a solid foundation for an organization and clear processes and standards for success, through the actual development work, and to the state where ‘what happens when problems arise’.
 

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
- MultipleMaintainers
- SuccessionPlan

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
- SecurityResponsive

4. **Respond to vulnerabilities:** Organizations should identify residual vulnerabilities in their software releases and have process and accountability to respond appropriately to those vulnerabilities and prevent similar ones from occurring in the future.
- CleanReleaseAvailable
- FixedVulnerabilities
- DependenciesAreManaged
- KnownVulnerabilitiesURL
