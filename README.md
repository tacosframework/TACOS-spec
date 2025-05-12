# TACOS-spec
TACOS Framework v2

This spec has been revised in partnership with an ongoing open source maintainer research study. The primary security tools in use today by maintainers include free automated dependency update tools, static analysis tools, and some fuzzing usage. The maintainers we've spoken with so far are taking the security of their source code and build pipelines seriously with a blend of automation, ecosystem-specific packaging tools, and a variery of best practices that are shared within ecosystems. There is still a noticeable lack of reliable income allocated to independent open source maintainers who require a more professional suite of security tools, or additional time to recruit and upskill new community members to contribute to security and maintenance work. This shows up noticeably in a lack of available community members for code reviews. Finally, maintainers have told us some of the ways that they evaluate new dependency considerations, as well as new contributions. We are iterating to reflect key project metrics that signal security and long-term outlook risk.

## Overview
TACOS is a framework for understanding and evaluating  the secure software development practices of open source packages. Its purpose is to identify gaps and opportunities to support the packages and people that underpin our shared software supply chains. It can be used to clarify where an organization should proactively invest in its open source dependencies, or choose to stop using a package. It can also be used to proactively assess any open source package for alignment with the risk profile of where the package may be used. 

The result of collecting this data is a machine-readable file that is complementary to other software security supply chain documents, such as Software Bills of Materials (SBOMs), Vulnerability Disclosure Reports (VDR), and Vulnerability Exploitability Exchange (VEX) documents. We call this point in time document an attestation report. It is a record of what secure development practices were known and unknown at the time of its creation for a particular package. 

## TACOS statements
At the heart of TACOS attestation reports are statements about the open source software development practices of an upstream open source software package. These statements are generally made at the package level, with statements about specific versions possible in a given attestation report (see below).

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
|Field   |Type   |Possible Values   |Description   |
|:-|:-|:-|:--|
|PackageName   |Metadata   |String   |Canonicalized package name   |
|PackagePlatform   |Metadata   |String   |Canonicalized package manager for the package   |
|PURL   |Metadata   |[PURL](https://github.com/package-url/purl-spec#some-purl-examples)   |PURL (package URL) for the package   |
|UpstreamRepositoryURL   |Metadata   |URL   |Validated URL for source code in order to perform  assessments, and record changes in its dependencies   |
|SPDXLicenseLatestRelease   |String   |SPDX expression   |Normalized, SPDX formatted licensing metadata   |
|SPDXLicenseLatestSource   |Metadata   |String   |URL of licensing metadata source (URL will either be identified through scraping source repositories and package managers, or manually verified)   |
|LatestStableRelease   |Metadata   |String   |Point release, mapped in the correct versioning scheme to show latest stable (no alpha, RC, etc.) release from upstream package managers   |
|PackageLifecycleStatus   |Attestation   |Array of Objects|"NOASSERTION"   |Validated data on the package that may be signaling that it will be abandoned, deprecated, or moved to EOL   |
|PackageLifecycleStatusPackageStatus.[]Status   |Attestation   |String   |One of "Active", "Inactive", "Deprecated", "Removed", or "EOL"   |
|PackageLifecycleStatusPackageStatus.[]EvidenceURL   |Metadata   |URL   |URL of package lifecycle evidence, such as commit and issue velocity, PR cycle time   |
|PackageLifecycleStatusPackageStatus.[]LastModifiedDateTimeUTC   |Metadata   |Timestamp   |Logged time of manual validation in UTC   |
|SBOM   |Metadata   |URL   |Referenceable URL for SBOM provided by maintainer or packaging tools at build time    |
|PackageManagerMFAEnabled   |Attestation   |"True"\|"False"\|"NOASSERTION"   |**Maintainer attests** to using multifactor authentication when accessing the build and release environment for software   |
|SourceRepoMFAEnabled   |Attestation   |"True"\|"False"\|"NOASSERTION"    |**Maintainer attests** to using multifactor authentication when accessing the development environment for software   |
|DocumentedBuildPractices   |Attestation   |"True"\|"False"\|"NOASSERTION"    |**Maintainer attests** to build process used while developing the software   |
|FixedVulnerabilities   |Attestation   |"True"\|"False"\|"NOASSERTION"    |This package's latest release includes fixes for all known vulnerabilities. |
|DependenciesAreManaged   |Attestation   |"True"\|"False"\|"NOASSERTION"    |The dependencies of this package's latest release all have no known vulnerabilities in their latest releases  |
|KnownVulnerabilitiesURL   |Metadata   |URL   |URL listing all vulnerabilities that affect the package and its dependencies   |
|PackageSecurityPolicyURL   |Metadata   |URL   |URL of active security policy with clear steps for coordinated disclosure of vulnerabilities. Ideally includes maintainer verification on which release streams are receiving security updates   |
|PackageSecurityContact   |Metadata   |URL   |URL of active security contact in the event of a vulnerability   |
|SecurityResponsive   |Attestation   |"True"\|"False"\|"NOASSERTION"    |Identifies whether the project has demonstrated activity indicating that a response to security issues is likely.    |
|MultipleMaintainers   |Attestation   |"True"\|"False"\|"NOASSERTION"    |Identifies whether the project has multiple maintainers to ensure package and community sustainability   |
|SuccessionPlan   |Attestation   |"True"\|"False"\|"NOASSERTION"    |**Maintainer attestation** that a project has a succession plan in place when a maintainer is ready to stop maintaining the package   |
|CodeReviewPractice   |Metadata   |"True"\|"False"\|"NOASSERTION"    |Consistent review of PRs in order to address bugs or vulnerabilities sooner, and also any potential contributions from bad actors   |
|FuzzingPractice   |Metadata   |"True"\|"False"\|"NOASSERTION"    |Assessing the practice of feeding random data in to expose exploitable bugs   |
|SAST   |Attestation   |"True"\|"False"\|"NOASSERTION"    |**Maintainer attestation** that a project is using a Static Analysis tool   |
|ContributionGuidelines   |Metadata   |URL   |URL of contribution guidelines for the package  |
|IncomeStreams   |Metadata   |Array of Objects\|"NOASSERTION"    |Package has validated income streams including lifting income from Tidelift, corporate backing, foundation, or variable/community backing   |Manually verified OR maintainer attested metadata   |
|IncomeStreams.[]Type   |Metadata   |String   |Any of "Lifted", "Corporate", "Foundation", "Variable", or "None"   |   |
|IncomeStreams.[]EvidenceURL   |Metadata   |URL   |URL of the income stream evidence   |   |
|IncomeStreams.[]LastModifiedDateTimeUTC   |Metadata   |Timestamp   |Logged time of manual validation   |   |


### CISA attestation form mappings
|Attestation Requirements   |Related EO 14028 Subsection   |Related SSDF Practices and Tasks    |TACOS framework   |
|:--|:-|:-|:--|
|1) The software was developed and built in secure environments. Those environments were secured by the following actions, at a minimum:   |4e(i)   |[See rows below]   |[See rows below]   |
|a) Separating and protecting each environment involved in developing and building software;   |4e(i)(A)   |PO.5.1   |DocumentedBuildPractices  |
|b) Regularly logging, monitoring, and auditing trust relationships used for authorization and access: <br />i) to any software development and build environments; and <br />ii) among components within each environment;    |4e(i)(B)   |PO.5.1   |DocumentedBuildPractices<br />NoBinariesInRepository<br />CodeReviewPractice  |
|c) Enforcing multi-factor authentication and conditional access across the environments relevant to developing and building software in a manner that minimizes security risk;    |4e(i)(D)   |PO.5.1   |DocumentedBuildPractices<br />PackageManagerMFAEnabled<br />SourceRepoMFAEnabled  |
|d) Taking consistent and reasonable steps to document, as well as minimize use or inclusion of software products that create undue risk, within the environments used to develop and build software;     |4e(i)(D)   |PO.5.1, PO.5.2   |SBOM<br />CodeReviewPractice  |
|e) Encrypting sensitive data, such as credentials, to the extent practicable and based on risk;     |4e(i)(E)    |PO.5.2   |DocumentedBuildPractices  |
|f) Implementing defensive cyber security practices, including continuous monitoring of operations and alerts and, as necessary, responding to suspected and confirmed cyber incidents;      |4e(i)(F)    |PO.3.2, PO.3.3, PO.5.1, PO.5.2   |SecurityResponsive<br />IncomeStreams  |
|2) The software producer has made a good-faith effort to maintain trusted source code supply chains by: <br />a) Employing automated tools or comparable processes; and <br />b) Establishing a process that includes reasonable steps to address the security of third-party components and manage related vulnerabilities;      |4e(iii)    |PO 1.1, PO.3.1, PO.3.2, PO.5.1, PO.5.2, PS.1.1, PS.2.1, PS.3.1, PW.4.1, PW.4.4, PW 7.1, PW 8.1, RV 1.1   |SDLCPolicyURL<br />SDLCEvidenceDataURL<br />CleanReleaseAvailable<br />DependenciesAreManaged<br />FixedVulnerabilities<br />FuzzingPractice<br />IncomeStreams  |
|3) The software producer maintains provenance data for internal and third-party code incorporated into the software;       |4e(vi)    |PO.1.3, PO.3.2, PO.5.1, PO.5.2, PS.3.1, PS.3.2, PW.4.1, PW.4.4, RV.1.1, RV.1.2   |SBOM<br /> |
|4) The software producer employed automated tools or comparable processes that check for security vulnerabilities. In addition:<br />a) The software producer ensured these processes operate on an ongoing basis and, at a minimum, prior to product, version, or update releases and <br />b) The software producer has a policy or process to address discovered security vulnerabilities prior to product release; and <br />c) The software producer operates a vulnerability disclosure program and accepts, reviews, and addresses disclosed software vulnerabilities in a timely fashion.       |4e(iv)    |PO.4.1, PO.4.2, PS.1.1, PW.2.1, PW.4.4, PW.5.1, PW.6.1, PW.6.2, PW.7.1, PW.7.2, PW.8.2, PW.9.1, PW.9.2, RV.1.1, RV.1.2, RV.1.3, RV.2.1, RV.2.2, RV.3.3    |SDLCPolicyURL<br />PackageSecurityPolicyURL<br />PackageSecurityContact<br />SecurityResponsive<br />CleanReleaseAvailable<br />SDLCEvidenceDataURL<br />IncomeStreams  |
