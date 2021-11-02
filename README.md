# Dedicated Hosts for macOS Preview Documentation
The purpose of this repository is to walk you through the details of the Dedicated Hosts for macOS preview on CircleCI.
## What are dedicated host resources on CircleCI?
The dedicated host resource class is a new macOS option for those developing, building, testing, and signing iOS, iPadOS, macOS, WatchOS, and tvOS applications using the Xcode IDE. These dedicated resources provide an isolated environment for increased security. This resource class requires a 24-hour minimum lease and runs on Intel-based Mac hardware. 

The identifier for the dedicated host resource is `metal` and supports Xcode v12.2.0 - 13.0.0 (see links below for image details).
### Supported Xcode Images
* [Xcode 13.0.0](https://circle-macos-docs.s3.amazonaws.com/image-manifest/cci-macos-production-1977/index.html)
* [Xcode 12.5.1](https://circle-macos-docs.s3.amazonaws.com/image-manifest/cci-macos-production-1964/index.html)
* [Xcode 12.4.0](https://circle-macos-docs.s3.amazonaws.com/image-manifest/cci-macos-production-1970/index.html)
* [Xcode 12.3.0](https://circle-macos-docs.s3.amazonaws.com/image-manifest/cci-macos-production-1971/index.html)
* [Xcode 12.2.0](https://circle-macos-docs.s3.amazonaws.com/image-manifest/cci-macos-production-1975/index.html)
### Known Limitations
* Does not currently support test splitting or parallelism
* Only supports a concurrecy limit of 1
* Will not work with the [CircleCI macOS orb](https://circleci.com/developer/orbs/orb/circleci/macos)
* Interface testing is not supported
* Open source projects are not supported (coming soon - need to patch a security vulnerability)
## Pricing and Specs
Once a dedicated host has been allocated, you will have exclusive access to it for a minimum of 24 hours. If the dedicated host is already in use when a job is kicked off, an additional dedicated host will be reserved (with its own 24 hour lease window). Each account can currently have a maximum of three concurrent dedicated hosts. Any time over the initial 24 hours that a dedicated host is in use will be charged a per-minute overage rate (see table below for pricing details).

|Resource Class Name|vCPU|Memory|Storage|Credits per 24hr Lease|Overage Credits/Minute
|---|---|---|---|---|---|
|`metal`|12|32 GiB|200 GiB|TBD|TBD

## Example of a CircleCI config using macOS dedicated host resources
```yaml
# .circleci/config.yml
version: 2.1
jobs: # a basic unit of work in a run
  build-and-test: # your job name
    macos:
      xcode: 12.5.1 # indicate our selected version of Xcode
    resource_class: metal # dedicated host, with 24-hour billing
    steps: # a series of commands to run
      - checkout  # pull down code from your VCS
      - run: bundle install
      - run:
          name: Fastlane
          command: bundle exec fastlane $FASTLANE_LANE
      - store_artifacts:
          path: output
      - store_test_results:
          path: output/scan
          
workflows:
  build-test:
    jobs:
      - build-and-test
```
## How to provide feedback & frequently asked questions
We will be sending a short, 2-question survey each week to gather feedback. You may also [open an issue](https://github.com/CircleCI-Public/macos-dedicated-host-preview-docs/issues) with any feedback or to report any issues that you encounter.
### FAQ
* Is there an Apple Silicon option for dedicated hosts?
  * We plan to support Apple Silicon hardware in the future, but that will not be included in this closed preview.
* Why is there a 24-hour minimum?
  * Apple released an [updated end-user license agreement (EULA)](https://www.apple.com/legal/sla/docs/macOSBigSur.pdf) along with the release of Big Sur in November 2020, which requires cloud providers to lease Apple hardware to no more than one customer for a minimum of 24 hours.
* How does a dedicated host differ from the other macOS resources on CircleCI?
  * Our other macOS resources are run on isolated virtual machines, which means that multiple customers can be using VMs on the same host. Dedicated hosts provide you exclusive access to an entire host, without worrying about sharing resources with other customers.
