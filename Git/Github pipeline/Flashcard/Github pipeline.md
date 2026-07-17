#flashcards/Github-Pipeline

H

Where must a GitHub Actions workflow file be located in an iOS repository to be recognized by the pipeline?
?
In the `.github/workflows/` directory at the root of the repository as a `.yml` or `.yaml` file.

How do you specify the environment to ensure your pipeline runs on a macOS machine with Xcode installed?
?
Use the `runs-on` keyword, setting it to a specific macOS runner (e.g., `runs-on: macos-14` or `macos-latest`), which comes pre-installed with Apple's development tools.

As a senior engineer ensuring build consistency, how do you enforce a specific Xcode version in a GitHub Action?
?
You can use the `DEVELOPER_DIR` environment variable to point to the correct Xcode app path (e.g., `/Applications/Xcode_15.2.app/Contents/Developer`), or use an action like `maxim-lobanov/setup-xcode`.

How do you configure a workflow to trigger only when a pull request targets the `main` branch?
?
Use the `on` key with `pull_request` and define the `branches` filter: `on: pull_request: branches: [ "main" ]`.

What is the standard GitHub Action used to clone your iOS repository into the runner environment before building?
?
The `actions/checkout` action (e.g., `uses: actions/checkout@v4`).

How do you implement caching for Swift Package Manager (SPM) dependencies to speed up CI build times?
?
Use the `actions/cache` action, setting the path to `.build/` or `~/Library/Developer/Xcode/DerivedData`. Use a cache key based on the hash of your `Package.resolved` file to automatically invalidate it when dependencies change.

Where should you store sensitive information like App Store Connect API keys or Match passwords for your pipeline?
?
In GitHub Repository Secrets (or Environment Secrets). They are then accessed securely in the YAML file using the `${{ secrets.SECRET_NAME }}` syntax.

What is the most robust way to handle iOS code signing in a GitHub Actions pipeline without committing certificates directly to the repo?
?
Use Fastlane Match combined with a GitHub personal access token (PAT) to access a private certificates repository, decrypting them dynamically on the CI runner using a Match password stored in GitHub Secrets.

What native command-line tool is invoked within a workflow step to build and test an iOS project?
?
`xcodebuild`. It is typically invoked directly via shell commands or through a wrapper like Fastlane (`scan` for testing, `gym` for building).

If your pipeline builds an `.ipa` or generates test logs, how do you make those files available for download after the workflow finishes?
?
Use the `actions/upload-artifact` action, specifying the `path` to the generated `.ipa` or log folder and giving the artifact a `name`.

Why would a senior developer choose to wrap `xcodebuild` commands in Fastlane within a GitHub Actions workflow?
?
Fastlane abstracts away the complexity of `xcodebuild` flags, standardizes the build process between local and CI environments, and simplifies integrations with App Store Connect and code signing tools.

How do you configure a workflow to automatically cancel previous in-progress runs when a developer pushes a new commit to the same PR?
?
Use the `concurrency` key at the workflow or job level, grouping by `${{ github.ref }}` or `${{ github.head_ref }}`, and set `cancel-in-progress: true`.

Which trigger allows QA or project managers to manually trigger a TestFlight build from the GitHub Actions UI?
?
The `workflow_dispatch` event. It can also be configured to accept custom input parameters (e.g., specifying a branch, environment, or build flavor).

Why is it strongly recommended to specify a custom Derived Data path when running `xcodebuild` in CI?
?
It isolates the build environment, ensures predictable paths for retrieving test logs, coverage reports, and build artifacts, and prevents caching conflicts between different CI jobs.

How can you prevent code duplication if multiple iOS apps in your organization share the exact same build and test pipeline setup?
?
Create a Reusable Workflow by triggering it with `on: workflow_call`. Other repositories can then reference this central workflow using the `uses` keyword, passing in necessary `inputs` and `secrets`.