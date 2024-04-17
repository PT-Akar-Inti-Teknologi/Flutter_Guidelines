# Project Setups

## Git Repository

For creating a Git repository, it's recommended to reach out to our DevOps [Helpdesk](https://helpdesk.akarinti.tech/support/tickets/new) to initiate the process. Make sure to mention to use this repository as a template project. If the repository has already been created without using the specified template, you can clone the repository and start your project from there.

---

## FVM

FVM, or Flutter Version Management, is a command-line tool designed to simplify the process of managing multiple versions of the Flutter SDK on your development machine. It provides a convenient way to switch between different Flutter SDK versions, ensuring compatibility with your Flutter projects.

### Why Use FVM?

- Multiple Flutter Projects: If you work on multiple Flutter projects simultaneously or collaborate on projects with different Flutter SDK requirements, FVM helps you manage the Flutter SDK versions for each project separately.

- Version Consistency: FVM allows you to ensure version consistency across different development environments and team members, preventing compatibility issues that may arise from using different Flutter SDK versions.

- Experimentation and Testing: FVM enables you to experiment with newer or older Flutter SDK versions without affecting your main development environment. This is particularly useful for testing compatibility with different Flutter releases or trying out new features.

### How to Use FVM

- Installation: You can install FVM using Flutter itself or via package managers like Homebrew or Chocolatey, depending on your operating system.

- Managing SDK Versions: Once installed, you can use FVM commands to install, list, and switch between different Flutter SDK versions. For example:

  - fvm install <version>: Installs a specific Flutter SDK version.
  - fvm use <version>: Sets the active Flutter SDK version for the current terminal session.
  - fvm list: Lists all installed Flutter SDK versions.

- Project Configuration: To associate a specific Flutter SDK version with a project, navigate to the project directory in your terminal and use fvm use <version> to set the desired SDK version. FVM will create a .fvm file in the project directory to store this configuration.
- Updating Flutter Version: If you need to upgrade flutter version just in case because some library needs it, run fvm use <version_number> --force inroot project terminal. for example fvm use 3.10.5 --force. Then notice all team member to run fvm install after they already pull latest changes.

> for more detailed on how to use fvm you could go to [fvm official doc](https://fvm.app/)

### IDE Setup

For more complete guide or extra features, see this link: https://fvm.app/docs/getting_started/configuration/#ide

#### VSCode

- open or create settings.json in .vscode directory
- make sure to add these to the json config:

```json
{
  "dart.flutterSdkPath": ".fvm/flutter_sdk",
  // Remove .fvm files from search
  "search.exclude": {
    "**/.fvm": true
  },
  // Remove from file watching
  "files.watcherExclude": {
    "**/.fvm": true
  },
  "editor.codeActionsOnSave": {
    "source.fixAll": "explicit",
    "source.organizeImports": "explicit"
  }
}
```

#### Android Studio

- Every time you open the project, make sure you change “Flutter SDK” path to /.fvm/flutter_sdk
- see link above to exclude .fvm/flutter_sdk from being scanned by IDE

---

## Melos

Melos is a powerful and flexible tool for managing Dart and Flutter monorepos. It simplifies the process of working with multiple packages within a single repository, providing features for dependency management, versioning, testing, and more.

### Why Use Melos?

- Monorepo Management: Melos streamlines the management of monorepos containing multiple Dart or Flutter packages. It provides a unified workflow for developing, testing, and releasing packages within the monorepo.

- Dependency Management: Melos simplifies dependency management between packages within the monorepo. It ensures that package dependencies are correctly resolved and synchronized across packages, eliminating version conflicts and ensuring compatibility.

- Consistent Versioning: Melos facilitates consistent versioning across all packages within the monorepo. It provides tools for managing package versions, ensuring that all packages are versioned consistently based on predefined rules.

- Automated Testing: Melos integrates with popular testing frameworks like flutter test and dart test, enabling automated testing of packages within the monorepo. It supports parallel testing and customizable test configurations.

- Release Automation: Melos automates the release process for packages within the monorepo. It provides tools for generating release notes, tagging releases, and publishing packages to package repositories like Pub.dev or custom registries.

### How to Use Melos

- Installation: You can install Melos globally using Dart's package manager, type in dart pub global activate melos in your terminal to activate melos on your workstation

- Configuration: Create a melos.yaml configuration file at the root of your monorepo to define the workspace structure, package settings, versioning rules, and other configurations.

- to check on what melos commands that are available on your project or if you want to add new commands you could access it in melos.yaml file on the root of your project

> for more info about melos you could read it [here](https://melos.invertase.dev/~melos-latest)

---

## Env

### Why use Env?

The .env file, short for "environment," serves a vital role in storing configuration variables and sensitive information necessary for your application to function seamlessly across different environments, including development, testing, and production.
we are using [envied package](https://pub.dev/packages/envied) to help us set up and generate our environment file

- Environment-specific configuration: Tailor settings according to each environment's requirements, such as varying database connection strings, API keys, or logging levels.
- Secure storage of sensitive data: Safeguard passwords, API keys, or access tokens securely within the .env file, minimizing security risks associated with hardcoding such information directly into the codebase.
- Ease of configuration: Simplify configuration management by storing variables in the .env file, allowing for easy modification without altering the codebase. This facilitates smoother deployment across diverse environments and simplifies collaboration among team members.
  Compliance adherence: Meet compliance standards and best practices by avoiding hardcoded sensitive information in the codebase, thereby enhancing security and mitigating potential risks.

### How to create one

1. Create .env.dev and .env.prod files at the root of your project. Refer to the .env.example file for guidance on adding new variables to your environment file. If necessary, edit env/lib/env.dart to accommodate new environment variables for your project.
2. Execute ./build_env.sh to generate the environment file for your project. You'll be prompted to select either the development or production environment for your project.

   > example of .env.dev

   ```
   FLAVOR="development"
   APP_NAME="Pakuwon"
   APP_KEY="p4kuw0nm3mb3r5h1p"
   BASE_URL="https://pakuwon-be.sandbait.work/"

   ```

   > example of env.dart file

   ```
   import 'package:envied/envied.dart';

   part 'env.g.dart';

   @Envied(path: '../.env')
   abstract class Env {
     const Env._();
     @EnviedField(varName: 'APP_NAME', obfuscate: true)
     static final String appName = _Env.appName;
     @EnviedField(varName: 'APP_KEY', obfuscate: true)
     static final String appKey = _Env.appKey;
     @EnviedField(varName: 'BASE_URL')
     static const String baseUrl = _Env.baseUrl;
     @EnviedField(varName: 'FLAVOR')
     static const String flavor = _Env.flavor;
   }

   ```

> During early development stages, you can populate the env.prod file with temporary data or copy environment variables declared in .env.dev.

> Remember, we gitignore the .env file and its generated files. Therefore, if you join a project in progress, you can obtain the required data and variables from your colleagues.
