# JOSH
JOSH(5)                  System Formats Manual                 JOSH(5)

NAME
       josh - JSON-based declarative system state specification

SYNOPSIS
       environment.josh.json

DESCRIPTION
       JOSH describes the desired state of a computing environment
       using versioned JSON documents.

       A JOSH recipe may describe operating system requirements,
       packages, runtimes, services, applications, databases,
       artifacts, dependencies, configuration requirements and
       verification conditions.

       JOSH does not itself execute commands.

       Executors consume JOSH recipes, compare desired state with
       observed target state, produce a plan, optionally apply
       permitted changes, and verify the resulting state.

CORE PRINCIPLES
       Declarative
              Describe what must be true rather than a sequence
              of shell commands.

       Idempotent
              Reapplying a satisfied recipe should normally cause
              no change.

       Verifiable
              Resources should define how successful state can be
              confirmed.

       Auditable
              Evaluation and changes must produce evidence.

       Explainable
              Requirements should record their source and reason
              where available.

       Safe
              Executors must distinguish planning, application and
              destructive operations.

DOCUMENT STRUCTURE
       josh_version
              Version of the JOSH specification.

       metadata
              Recipe identity, origin and generation information.

       environment
              Platform-level requirements.

       resources
              Desired system resources.

       artifacts
              External payloads referenced by resources.

       policies
              Constraints on executor behavior.

       verification
              Tests establishing successful state.

RESOURCE TYPES
       package
       runtime
       extension
       service
       file
       directory
       application
       database
       configuration
       artifact

RESOURCE STATE
       A resource may contain observed and desired state.

       observed
              State measured by discovery software.

       desired
              State required by the recipe.

       evidence
              Source supporting the requirement.

       dependencies
              Resources required before this resource.

       verify
              Conditions proving successful state.

EXECUTION MODEL
       A conforming executor SHOULD:

       1. Parse and validate the recipe.
       2. Inspect the target environment.
       3. Compare observed and desired state.
       4. Produce an execution plan.
       5. Request authorization where policy requires.
       6. Apply approved changes.
       7. Verify resulting state.
       8. Record an audit result.

       Executors MUST NOT interpret unknown resource types as
       successful operations.

FILES
       *.josh.json
              Conventional JOSH recipe filename.

EXAMPLE
       {
         "josh_version": "0.1",
         "resources": [
           {
             "type": "package",
             "name": "sqlite3",
             "desired": {
               "state": "present"
             }
           }
         ]
       }

SEE ALSO
       rickshaw(1)
       josh-validate(1)
       josh-plan(1)
       josh-apply(1)

AUTHORS
       B. Moore Solutions

PHILOSOPHY
       Declare the state.
       Explain the state.
       Verify the state.
