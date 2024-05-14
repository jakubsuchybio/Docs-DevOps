# Ideal "devops"

## Main Goals
- Everything inside repository (infrastructure as code)
  - step scripts
  - job scripts
  - pipeline definition
  - docker definition
- Minimal to NO vendor-lock-in
  - Vendors change pricing
  - Vendors deprecates features
  - Vendors might not have a specific feature
- Reproducible builds, even locally

## C# Current state
- Cake scripts
  - Goals fulfillment
    - steps scripts
    - job scripts
  - HOWEVER
    - We don't use staticly typed features of cake, because they can lack latest cli arguments
    - We use it only for creating dependency graph of different tasks (steps)
- Gitlab-CI.yaml
  - Goal fulfillment - pipeline definition
  - HOWEVER
    - managing artifacts is not straightforward
    - running jobs is costly, because it runs 2 docker instances (main+helper)
- Different docker definition repo
  - Goal fulfillment - docker definition
  - HOWEVER
    - Docker definition doesn't live in the same repo as main code => not easy to reproduce historic pipelines
      
## C# Ideal state
- Multiple dockers for multiple purposes which are as small as possible (not one docker which has 14GB)
- Docker definitions live in main repo
- Gitlab-CI.yaml serves only as "entrypoint" which only runs one job - Main C# pipeline with passed arguments for it to run fully
- C# scripts
  - Not based on Cake, but BullsEye, which also has task dependency functionality
    - It even has parallel tasks execution
  - Using SimpleExec for running cli commands
  - Running tasks in parallel has some challenges
    - Output logs will get scrambled
    - Some commands can't run in parallel (e.g. multiple msbuilds)
  - Solutions for challenges
    - Parallelization
      - sandboxie? => NO-GO, sandboxie can't run inside docker