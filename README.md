# JOSH — JSON Orchestration Schema & Hierarchy

```text
JOSH(5)                  System Formats Manual                 JOSH(5)

NAME
       josh - JSON Orchestration Schema & Hierarchy

SYNOPSIS
       *.josh.json

DESCRIPTION
       JOSH is a versioned, JSON-based declarative specification
       for describing the desired state of a computing environment.

       JOSH documents describe what should exist, how resources
       relate to one another, why they are required, and how the
       resulting state can be verified.

       JOSH is not a shell scripting language.

       JOSH does not primarily describe a sequence of commands.
       Instead, it describes system state in a structured format
       that can be inspected, validated, compared, planned,
       executed by compatible tools, and verified.

       A JOSH document may describe:

              operating system requirements
              architecture requirements
              packages
              runtimes
              runtime extensions
              files and directories
              services
              applications
              databases
              configuration
              artifacts
              dependencies
              verification requirements
              execution policy
              evidence and provenance

       Software that understands JOSH may compare the declared
       desired state with the observed state of a target system
       and determine what, if anything, must change.

PURPOSE
       JOSH provides a machine-readable contract describing what
       an environment should contain.

       It separates desired state from the specific commands used
       to achieve that state.

       For example, rather than declaring:

              apt install php8.4-sqlite3

       JOSH may declare:

              {
                "type": "extension",
                "runtime": "php",
                "name": "sqlite3",
                "desired": {
                  "state": "present"
                }
              }

       The executor is responsible for determining how that state
       should be achieved on the target platform.

CORE PRINCIPLES

       Declarative
              JOSH describes what should be true rather than a
              rigid sequence of shell commands.

       Idempotent
              Re-evaluating a system already satisfying a JOSH
              document should normally produce no change.

       Verifiable
              Desired state should be capable of being tested
              after execution.

       Explainable
              Requirements may include evidence, reason, source,
              and confidence information explaining why they exist.

       Auditable
              Evaluation, planning, execution, and verification
              should produce an audit trail.

       Portable
              Desired state should avoid unnecessary dependence
              on one execution implementation.

       Safe
              Planning, execution, destructive actions, and
              verification should remain clearly separated.

STATE MODEL
       JOSH distinguishes between several forms of state.

       OBSERVED
              What was discovered on a source or target system.

       DESIRED
              What the environment is expected to satisfy.

       EVIDENCE
              Why a desired-state requirement exists and where
              that information came from.

       RESULT
              The state measured after execution or remediation.

       Example:

              {
                "type": "runtime",
                "name": "php",

                "observed": {
                  "version": "8.4.24"
                },

                "desired": {
                  "constraint": "^8.3"
                },

                "evidence": {
                  "source": "composer.json",
                  "reason": "Application PHP requirement",
                  "confidence": 100
                }
              }

       Observed state is not automatically considered desired state.

       A source machine may contain a specific implementation or
       version even when the actual requirement permits a broader
       compatible range.

DOCUMENT STRUCTURE
       A JOSH document may contain the following top-level concepts.

       josh_version
              Version of the JOSH specification used by the
              document.

       metadata
              Document identity, origin, generation information,
              timestamps, producer information, and descriptive
              data.

       environment
              Platform-level constraints such as operating system,
              architecture, capabilities, or runtime expectations.

       resources
              Desired system components and their relationships.

       artifacts
              External payloads required by resources, such as an
              application archive or database backup.

       policies
              Rules controlling permitted behavior and execution.

       verification
              Conditions used to determine whether the resulting
              environment satisfies the declared state.

RESOURCE TYPES
       Initial JOSH resource types may include:

              package
              runtime
              extension
              service
              file
              directory
              configuration
              application
              database
              artifact

       Future versions may define additional resource types.

RESOURCE FIELDS
       Resources may contain fields such as:

       type
              Resource category.

       name
              Human-readable or provider-specific resource name.

       desired
              Required target state.

       observed
              Previously discovered state, if known.

       evidence
              Source, reason, confidence, or provenance supporting
              the requirement.

       dependencies
              Other resources that should be satisfied first.

       provider
              Provider or implementation associated with the
              resource.

       verify
              Conditions proving that desired state was achieved.

       policy
              Resource-specific execution restrictions.

DEPENDENCIES
       Resources may depend on other resources.

       For example:

              Laravel application
                     |
                     +-- PHP runtime
                     |
                     +-- PHP extensions
                     |
                     +-- Composer
                     |
                     +-- database
                     |
                     +-- web server

       Dependency information allows an executor to build an
       ordered plan without requiring the JOSH document to become
       a procedural shell script.

ARTIFACTS
       JOSH may reference external artifacts.

       Examples include:

              application archives
              verified database backups
              configuration templates
              certificates
              packages
              deployment payloads

       JOSH does not require artifact data to be embedded directly
       in the JSON document.

       Example:

              {
                "type": "database",
                "name": "bingbong",

                "desired": {
                  "engine": "sqlite"
                },

                "artifact": {
                  "path": "databases/bingbong/database.sqlite",
                  "sha256": "..."
                }
              }

EXECUTION MODEL
       JOSH itself does not execute commands.

       A compatible executor may consume a JOSH document.

       A conforming executor SHOULD:

              1. Parse the JOSH document.

              2. Validate the document against the supported
                 JOSH specification.

              3. Inspect the target environment.

              4. Compare observed target state with desired state.

              5. Resolve dependencies.

              6. Produce a human-readable and machine-readable plan.

              7. Identify operations requiring authorization.

              8. Apply explicitly permitted changes.

              9. Verify resulting state.

             10. Record an audit result.

       Executors MUST NOT silently treat unknown resource types or
       unsupported requirements as successfully satisfied.

PLAN BEFORE APPLY
       JOSH is intended to support a clear separation between
       inspection, planning, execution, and verification.

       Typical lifecycle:

              DECLARE
                 |
                 v
              VALIDATE
                 |
                 v
              OBSERVE
                 |
                 v
              COMPARE
                 |
                 v
              PLAN
                 |
                 v
              APPROVE
                 |
                 v
              APPLY
                 |
                 v
              VERIFY
                 |
                 v
              AUDIT

EXAMPLE
       A simplified JOSH environment may look like:

              {
                "josh_version": "0.1",

                "metadata": {
                  "name": "BingBong DEV Environment",
                  "generated_by": "rickshaw"
                },

                "environment": {
                  "os": {
                    "family": "debian",
                    "architecture": "amd64"
                  }
                },

                "resources": [
                  {
                    "type": "runtime",
                    "name": "php",
                    "desired": {
                      "constraint": "^8.3"
                    },
                    "observed": {
                      "version": "8.4.24"
                    }
                  },

                  {
                    "type": "extension",
                    "runtime": "php",
                    "name": "sqlite3",
                    "desired": {
                      "state": "present"
                    }
                  },

                  {
                    "type": "service",
                    "name": "apache2",
                    "desired": {
                      "enabled": true,
                      "state": "running"
                    }
                  },

                  {
                    "type": "application",
                    "name": "BingBong",
                    "provider": "laravel",
                    "desired": {
                      "path": "/var/www/bingbong"
                    }
                  }
                ]
              }

PRODUCERS
       A producer creates JOSH documents.

       Producers may include discovery, backup, migration, inventory,
       provisioning, or configuration-management systems.

       Rickshaw is intended to be an early JOSH producer.

       Rickshaw may inspect an existing system and translate its
       findings into a proposed JOSH reconstruction document.

       Rickshaw may preserve:

              observed state
              desired requirements
              evidence
              restore artifacts
              version constraints
              dependencies
              verification requirements

CONSUMERS
       A consumer reads JOSH documents.

       Consumers may include:

              validators
              planners
              provisioning engines
              restore engines
              configuration engines
              compliance tools
              audit systems

       A consumer does not necessarily need permission to modify
       the target system.

       Read-only consumers may use JOSH solely for validation,
       comparison, reporting, or planning.

HUMAN USE
       JOSH is designed primarily for machine consumption while
       remaining readable and inspectable by humans.

       Most end users should not need to write JOSH manually.

       Administrators, developers, integrators, and module authors
       may inspect or author JOSH documents directly.

       User interfaces may generate and consume JOSH without
       exposing the underlying document to the user.

FILE FORMAT
       JOSH documents are JSON.

       Recommended filename convention:

              <name>.josh.json

       Example:

              bingbong.josh.json

       JOSH does not define a new serialization syntax.

       Standard JSON parsers may read JOSH documents.

VERSIONING
       Each JOSH document MUST identify the specification version
       it expects.

       Example:

              {
                "josh_version": "0.1"
              }

       Consumers SHOULD reject unsupported major specification
       versions rather than attempting unsafe interpretation.

VALIDATION
       JOSH should have a published JSON Schema.

       A validator may eventually provide commands such as:

              josh validate environment.josh.json

       Validation should identify:

              syntax errors
              unknown fields where prohibited
              missing required fields
              unsupported resource types
              invalid dependency references
              invalid state definitions
              unsupported specification versions

COMMANDS
       Future JOSH tooling may provide commands such as:

              josh validate
                     Validate a JOSH document.

              josh inspect
                     Display a human-readable representation.

              josh compare
                     Compare desired state against a target.

              josh plan
                     Produce an execution plan.

              josh apply
                     Apply explicitly authorized changes.

              josh verify
                     Verify resulting state.

       These commands describe possible tooling and are not part
       of the JSON file format itself.

SECURITY
       JOSH documents should not be treated as inherently trusted.

       Consumers should validate documents before execution.

       Secrets SHOULD NOT be embedded directly in ordinary JOSH
       documents.

       Sensitive data should normally be referenced through
       protected secret providers or secure artifact mechanisms.

       Executors should distinguish between:

              read-only operations
              configuration changes
              package changes
              service changes
              destructive operations
              secret access

       Potentially destructive operations SHOULD require explicit
       authorization.

DESIGN PHILOSOPHY
       JOSH is intended to make system state:

              declarative
              explainable
              portable
              auditable
              verifiable

       The specification should remain smaller than the systems
       that implement it.

       JOSH describes the desired result.

       Executors decide how to safely achieve it.

PHILOSOPHY
       Declare the state.
       Explain the state.
       Verify the state.

SEE ALSO
       josh-validate(1)
       josh-plan(1)
       josh-apply(1)
       josh-verify(1)
       rickshaw(1)

AUTHORS
       B. Moore Solutions

       JOSH — JSON Orchestration Schema & Hierarchy
```
