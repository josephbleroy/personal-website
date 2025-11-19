# GRC Engineering Content Design

**Date**: 2025-11-18
**Author**: Joseph LeRoy
**Status**: Approved

## Overview

Personal documentation of GRC Engineering learning journey with AWS, focused on practical automation that reduces audit friction while improving security posture.

## Content Strategy & Vision

### Purpose
Document practical AWS GRC Engineering implementations that:
- Reduce friction before and during audits
- Improve both GRC outcomes and security posture
- Provide working automation code, not just theory

### Content Focus
Step-by-step technical tutorials showing how to implement compliance controls in AWS using Infrastructure-as-Code (OpenTofu). Each tutorial focuses on controls that overlap multiple frameworks (PCI DSS, SOC 2, etc.) to maximize value.

### Target Audience
Mixed AWS expertise levels:
- Beginners: Include quick refreshers for AWS concepts
- Advanced: Focus on compliance implementation and automation
- All levels: Practical, working code they can deploy

### Tutorial Scope
- **Format**: Focused and tactical (~1500-2000 words per post)
- **Scope**: ONE specific implementation per tutorial
- **Code**: Complete working OpenTofu code + essential snippets in posts
- **Visuals**: Architecture diagrams + AWS console screenshots

### Publishing Cadence
Bi-weekly (one tutorial every two weeks) - sustainable pace for research, implementation, testing, writing, and creating visuals.

### Differentiation
- Working OpenTofu code, tested in AWS
- Compliance mapping to multiple frameworks
- Validation/testing steps included
- Real-world context from PCI DSS and SOC 2 experience

## Tutorial Structure & Format

### Standard Tutorial Sections

1. **Introduction**
   - What you're implementing
   - Why it matters for GRC

2. **Prerequisites**
   - AWS account requirements
   - Tools needed (OpenTofu, AWS CLI, etc.)
   - Previous tutorials if part of series

3. **Architecture Overview**
   - AWS architecture diagram
   - What you're building
   - How components interact

4. **Implementation**
   - Step-by-step instructions
   - OpenTofu code with explanations
   - Compliance callout boxes throughout

5. **Validation/Testing**
   - How to verify the control is working
   - What to check in AWS console
   - Expected outputs

6. **Compliance Mapping**
   - Comprehensive table mapping implementation to controls
   - Evidence collection guidance
   - Audit preparation notes

7. **Conclusion**
   - Summary
   - Link to GitHub repo
   - Next steps/related tutorials

8. **References**
   - AWS documentation
   - Compliance framework links
   - Additional resources

### Code Presentation

**In Blog Posts**:
- Essential code snippets illustrating key concepts
- Inline with explanations
- Enough to follow along without leaving the post

**In GitHub Repository**:
- Complete, tested, working OpenTofu code
- Full deployment examples
- Cleanup/teardown scripts
- Tutorial-specific README with deployment instructions

**IaC Tool Focus**:
- **Primary**: OpenTofu (open-source Terraform fork)
- **Note**: Mention it's a Terraform fork, compatible syntax
- **Alternative**: Reference CloudFormation options where relevant

### Compliance Mapping Format

**Inline Callout Boxes** (throughout implementation):
```markdown
> **Compliance Note: PCI DSS 8.2.1, SOC 2 CC6.1**
>
> Enabling MFA for all users addresses requirements for multi-factor
> authentication. This control provides evidence for annual audits.
```

**Compliance Mapping Table** (dedicated section):

| Control Requirement | Framework(s) | Implementation | Evidence Collection |
|---------------------|--------------|----------------|---------------------|
| Multi-factor authentication | PCI DSS 8.3.1<br>SOC 2 CC6.1 | IAM Identity Center MFA policy | CloudTrail logs, MFA device list |
| Centralized access | PCI DSS 8.1.1<br>SOC 2 CC6.2 | Identity Center SSO | User directory, access logs |

**Framework Coverage**:
- **PCI DSS v4.0** - Specific requirement numbers
- **SOC 2 Trust Services Criteria** - Common Criteria (CC6.x, CC7.x)
- **Future**: NIST 800-53, CIS AWS Foundations Benchmark, ISO 27001

### Visual Elements

**Architecture Diagrams**:
- AWS architecture icons (standard)
- Tools: draw.io, Lucidchart, or similar
- One diagram per tutorial (Architecture Overview section)

**Screenshots**:
- Key AWS console views
- Guide readers through validation steps
- Show expected outputs

## Technical Stack & Tools

### Website Platform
- **Hugo**: Existing static site generator
- **Theme**: hello-friend-ng (current)
- **Hosting**: Current setup (GitHub Pages or similar)

### Infrastructure-as-Code
- **Primary**: OpenTofu
- **Version Pinning**: Lock versions for reproducibility
- **Alternative**: CloudFormation options referenced

### Code Repository
- **Name**: `aws-grc-engineering`
- **Platform**: GitHub (public)
- **Versioning**: Git tags for releases
- **Community**: GitHub Discussions and Issues

### Diagram Tools
- draw.io, Lucidchart, or similar
- AWS architecture icons
- Screenshot tools for console captures

### Content Workflow
1. Test OpenTofu code in AWS
2. Create diagrams and screenshots
3. Write tutorial in Markdown
4. Commit code to repo
5. Publish blog post
6. Cross-link blog ↔ repo

## Site Organization (Hugo)

**Implementation Note (2025-11-18)**: The landing page has been simplified to minimal content with automatic post listing. See `docs/plans/2025-11-18-minimal-grc-landing-page-design.md` and `docs/plans/2025-11-18-minimal-grc-landing-page-implementation.md` for details.

### Content Structure (Hybrid Approach)

**Posts Location**:
- All tutorials in `/content/posts/`
- Alongside existing technical/security content
- Existing permalink: `/posts/YYYY/MM/title/`
- Chronological timeline (natural for learning journey)

**Taxonomy Organization**:
- **Category**: "GRC Engineering"
- **Tags**: "AWS", "SOC2", "PCI-DSS", "IAM", "OpenTofu", "Access Control", etc.
- **Series**: "AWS Foundations for Compliance", "AWS Access Control for Compliance"

### Navigation & Discovery

**New Menu Item**: "GRC Engineering"

**Landing Page** (`/grc-engineering/`):
- Introduction to GRC automation focus
- Tutorial series organized by topic
- Frameworks covered (PCI DSS, SOC 2)
- Link to GitHub repository
- Brief bio/credibility (PCI DSS, SOC 2 experience)

**Landing Page Sections**:
1. Introduction
   - "Automating AWS compliance controls that reduce audit friction"
   - Personal learning journey + helping others

2. Tutorial Series
   - AWS Foundations for Compliance
   - AWS Access Control for Compliance
   - (Future series)

3. Frameworks Covered
   - PCI DSS v4.0
   - SOC 2 Trust Services Criteria
   - (Future additions)

4. Code Repository
   - Link to `aws-grc-engineering` on GitHub
   - Working OpenTofu code for all tutorials

### Reader Engagement
- **No blog comments system**
- **Footer note**: "Questions or improvements? Open an issue on GitHub"
- **Contact**: Existing email/Twitter links

## GitHub Repository Structure

### Repository Name
`aws-grc-engineering`

### Directory Structure
```
aws-grc-engineering/
├── README.md                          # Repo overview, link to blog
├── LICENSE                            # Open source license
├── .gitignore                         # Terraform/OpenTofu ignores
├── tutorials/
│   ├── 01-aws-organizations-setup/
│   │   ├── README.md                  # Tutorial-specific notes
│   │   ├── main.tf                    # OpenTofu main config
│   │   ├── variables.tf               # Input variables
│   │   ├── outputs.tf                 # Output values
│   │   ├── versions.tf                # OpenTofu version constraints
│   │   ├── terraform.tfvars.example   # Example values
│   │   └── diagrams/
│   │       └── architecture.png
│   ├── 02-iam-identity-center/
│   │   ├── README.md
│   │   ├── main.tf
│   │   └── ...
│   └── ...
├── modules/                           # Reusable modules (future)
│   └── ...
└── docs/
    └── CONTRIBUTING.md                # How to contribute
```

### README.md Contents
- Project description and purpose
- Link to blog: josephbleroy.com/grc-engineering
- How to use tutorials
- Prerequisites (AWS account, OpenTofu)
- Contributing guidelines
- License

### Tutorial-Specific README.md
- Link to blog post
- Quick deployment instructions
- Prerequisites
- Cleanup/teardown steps

### Versioning Strategy
- **Git tags**: `v1.0.0` for initial release
- **Updates**: New tags when AWS changes require updates
- **Blog notes**: "Code updated for AWS 2025 - see repo tag v2.0.0"

## Publishing Workflow

### Bi-Weekly Process

**Week 1 - Research & Implementation**:
1. Choose next tutorial topic
2. Research AWS service + compliance requirements
3. Build implementation in test AWS account
4. Write and test OpenTofu code
5. Validate controls (take screenshots)

**Week 2 - Documentation & Publishing**:
1. Create architecture diagrams
2. Write blog post (standard template)
3. Add compliance callout boxes and table
4. Organize code in GitHub repo folder
5. Create tutorial-specific README
6. Commit to `aws-grc-engineering` repo
7. Publish blog post on Hugo site
8. Cross-link blog ↔ repo
9. Share (optional)

### Content Maintenance

**Tracking Changes**:
- Monitor AWS service updates
- Track compliance framework revisions
- Watch for breaking changes

**Update Strategy**:
- Minor updates (typos): Update in place
- Code updates: New repo tag, update blog
- Major changes: Consider new tutorial, link from original

**Version Management**:
- Initial: Tag `v1.0.0`
- Minor: Update in place
- Major: New tag + blog update
- Breaking: New tutorial option

### Quality Checklist (Before Publishing)
- [ ] OpenTofu code tested in clean AWS account
- [ ] All screenshots current and accurate
- [ ] Compliance mapping verified against official docs
- [ ] Links working (blog ↔ repo)
- [ ] Cleanup/teardown instructions included
- [ ] Diagrams clear and professional
- [ ] Code formatted and commented

## First Tutorial Series: "AWS Foundations for Compliance"

### Series Goal
Build a compliant AWS foundation from scratch - the prerequisites for implementing specific security controls.

### Tutorial #1: AWS Organizations & Account Structure

**Topic**: Setting up AWS Organizations, creating OUs, joining accounts

**Why It Matters**: Foundation for centralized governance and compliance

**Implementation**:
- Create AWS Organization
- Set up OU structure (Security, Workloads, Sandbox)
- Join additional accounts to organization
- Enable AWS Organizations features (SCPs, trusted access)

**Compliance Mapping**:
- PCI DSS 12.1 (establish security policy)
- SOC 2 CC9.1 (risk assessment, governance)

**Prerequisites**:
- AWS root account
- Email addresses for additional accounts (optional)

### Tutorial #2: AWS IAM Identity Center Setup

**Topic**: Configure centralized SSO and identity provider integration

**Why It Matters**: Single point of access control, MFA enforcement, audit trail

**Implementation**:
- Enable IAM Identity Center (requires Organizations)
- Connect to identity provider (Azure AD, Okta, or AWS managed)
- Configure permission sets (admin, developer, auditor)
- Assign users to AWS accounts
- Enforce MFA policy
- Test SSO access

**Compliance Mapping**:
- PCI DSS 8.2.1, 8.3.1 (MFA, centralized authentication)
- SOC 2 CC6.1, CC6.2 (logical access controls)

**Prerequisites**:
- Tutorial #1 completed (AWS Organizations enabled)
- Identity provider (or use AWS managed directory)

### Future Tutorials (Access Control Series)
1. IAM Policy Boundaries and Least Privilege
2. Cross-Account Access Patterns for Compliance
3. Monitoring and Alerting on Privileged Access
4. Service Control Policies (SCPs) for Guardrails
5. Break-Glass Access Procedures

### Future Series Ideas
- **Logging & Monitoring for Compliance**
  - CloudTrail multi-account setup
  - Centralized logging with S3
  - CloudWatch alerting for compliance events

- **Data Protection & Encryption**
  - KMS key management strategies
  - S3 encryption at rest
  - Certificate management

- **Network Security & Segmentation**
  - VPC design for PCI DSS cardholder data
  - Security groups and NACLs
  - Network traffic monitoring

## Success Metrics

### Immediate Goals
- Publish Tutorial #1 within 2 weeks of starting
- Establish bi-weekly publishing cadence
- Complete "AWS Foundations for Compliance" series (2 tutorials)

### 6-Month Goals
- 12-13 tutorials published (bi-weekly)
- Complete 2-3 tutorial series
- GitHub repo with working code for all tutorials
- Community engagement (GitHub issues/discussions)

### Long-Term Vision
- Comprehensive AWS GRC automation resource
- Cover major compliance frameworks (PCI DSS, SOC 2, NIST, CIS)
- Reusable OpenTofu modules library
- Community contributions and feedback

## Next Steps

1. **Set up infrastructure**
   - Create GitHub repository `aws-grc-engineering`
   - Create GRC Engineering landing page on Hugo site
   - Set up taxonomy (category, tags, series)

2. **Prepare Tutorial #1**
   - Research AWS Organizations best practices
   - Build test implementation
   - Write OpenTofu code
   - Create diagrams

3. **Publish Tutorial #1**
   - Write blog post
   - Publish code to GitHub
   - Cross-link blog ↔ repo

4. **Establish rhythm**
   - Bi-weekly publishing schedule
   - Build backlog of tutorial ideas
   - Refine template based on feedback
