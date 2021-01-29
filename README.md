# Emulsify CLI

This repository contains markdown files that describe in detail the feature requirements and general purpose for the Emulsify CLI in it's 1.0 state. It also contains documentation around the organization patterns that the CLI will establish and work with.

## Term definitions (specification)

For more information on the keywords used in this documentation, please reference [RFC-2119](https://www.ietf.org/rfc/rfc2119.txt).

- **Component**: Collection of files that, when loaded into the correct system, construct a modular design element that can be used within a user interface.
- **Design System**: A series of components that can be used to compose larger user interfaces.
- **Emulsify**: Tool that allows for design systems to be defined such a way that they can be incorporated into a variety of different platforms in a consistent, orderly manner.
- **Emulsify System**: Design system that has been implemented in the Emulsify ecosystem.
- **Emulsify System Variant**: Variant of an Emulsify System that is allows for the system to be implemented within a specific platform, such as a Drupal, WordPress, Gatsby, or Next.
- **Emulsify System Structure**: Structure of a system, primarily folder organization patterns.
- **Emulsify Project**: A project (such as a theme) that uses the appropriate Emulsify System Variant for the given platform.
- **Emulsify CLI**: Command line interface that enables developers to initialize Emulsify projects, select an appropriate system and scaffold it within the project according to the system specification, and download/import components defined within the system.
- **MUST**: indicates that something is a non-negotiable requirement.
- **REQUIRED**: synonym of **MUST**.
- **MUST NOT**: inverse of **MUST**, indicating that the absence of something is a non-negotiable requirement.
- **SHOULD**: indicates that something is recommended, but not required.
- **SHOULD NOT**: inverse of **SHOULD**, indicating that something is not recommended, but tolerable.
- **CAN**: indicates that something is neither required, nor recommended.
- **OPTIONAL**: synonym of **CAN**.
- **IN QUESTION**: indicates that something is in question, and needs to be re-visited.

## Emulsify System

Emulsify Systems are design systems that are available within the Emulsify ecosystem. A design system has a single `system.emulsify.json` file with 3 responsibilities:

- Provide a name for the system.
- Provide a link to documentation, or an example implementation of the system.
- Define the variants that are available within the system, such as Drupal, WordPress, Gatsby, Next.

### Configuration

- **name** (REQUIRED): String name of the Emulsify System. MUST be machine-friendly, including only alphanumeric characters, and dashes (`-`).
- **homepage** (REQUIRED): Hyperlink to external resources, such as documentation or an example implementation of the system and it's components.
- **repository** (REQUIRED): String containing a link to the git repository containing this system. This is used by the Emulsify CLI to find components and metadata about the system.
- **structure** (REQUIRED): Array containing objects that define the structure of the system. This is used to define the primary component categories that MUST be implemented within each variant. Each structure object within this array MUST contain a machine-friendly `name` and a `description` that describes the structure's intended purpose. Each variant of the system MUST provide a path for every structure defined within the system.
- **variants** (REQUIRED): Array containing objects that describe each variant of the system. The objects can be full definitions of the variants, or references to a repository that contains the variant (along with a `variant.emulsify.json` file). For more information about variants and example variant configuration, see the variant documentation section of this document.

#### `system.emulsify.json`

```json
{
  "name": "cornflake",
  "homepage": "https://cornflake-ds.info",
  "repository": "git@github.com:cornflake-ds/cornflake.git",
  "structure": [
    {
      "name": "base",
      "description": "Base-level components used in virtually every higher-level component"
    },
    {
      "name": "atoms",
      "description": "Small components commonly used in higher-level components"
    },
    {
      "name": "molecules",
      "description": "Medium-sized components used as building blocks within a larger component"
    },
    {
      "name": "organisms",
      "description": "Large components that compose smaller components into a cohesive UI"
    },
    {
      "name": "pages",
      "description": "Entire pages composed of smaller components"
    }
  ],
  "variants": [
    {
      "platform": "wordpress",
      "repository": "git@github.com:cornflake-ds/cornflake-wordpress.git"
    },
    {
      "platform": "drupal9",
      "structureImplementations": [
        {
          "name": "base",
          "directory": "./components/00-base"
        },
        {
          "name": "atoms",
          "directory": "./components/01-atoms"
        },
        {
          "name": "molecules",
          "directory": "./components/02-molecules"
        },
        {
          "name": "organisms",
          "directory": "./components/03-organisms"
        },
        {
          "name": "pages",
          "directory": "./components/04-pages"
        }
      ],
      "components": [
        {
          "name": "card",
          "structure": "molecules",
          "description": "A card that can be used to create small sections"
        },
        {
          "name": "link",
          "structure": "atoms",
          "description": "Component that, when clicked, takes the user to a specified destination"
        }
      ],
      "requiredComponents": ["link"],
      "globalDirectories": [
        {
          "name": "asset",
          "path": "./assets",
          "description": "Contains shared assets required by components within the system"
        },
        {
          "name": "script",
          "path": "./js",
          "description": "Contains JavaScript that can be loaded in order to facilitate interactive behaviors within components."
        }
      ]
    },
    {
      "platform": "gatsby",
      "repository": "git@github.com:cornflake-ds/cornflake-gatsby.git"
    }
  ]
}
```

### Installation

Emulsify Systems can be implemented by installing a variant of the system that is compatible with the platform. The Emulsify CLI will attempt to determine the type of platform you are using and recommend a variant, but you can also manually select a variant.

```bash
# Running within a Drupal 9 project...

emulsify system install cornflake
  It looks like you are working on a "next" project. Would you like to install the "next" variant of the cornflake system? Y/n

  # if "no" is selected, or if the platform is undeterminable...
  The following variants are available:
    > drupal8
    > drupal9   <-
    > drupal10
    > gatsby
    > next
    > nuxt
    > wordpress
```

## Emulsify System Variant

Emulsify System Variants contain information about its system's implementation within a specific platform, such as Drupal. Variants also come with components, assets, and everything needed to use the design system within its targeted platform.

Variants can be fully defined within the `system.emulsify.json` file in the `variants` array, or they can be defined within their own repository with a `variant.emulsify.json` file, and referenced from within the `system.emulsify.json` file. In either case, the files and assets for a variant MUST live within the same repository as the variant definition.

### Configuration

- **system** (OPTIONAL): Can contain the name of the system to which the variant belongs. This allows for variant's systems to be easily identified if the variant is defined in a separate repository.
- **platform** (REQUIRED): String describing the platform for which the variant is intended, such as drupal9, wordpress, etc. (IN QUESTION: We might want to define an enum of possible values.)
- **repository** (REQUIRED if the variant has it's own repository): String containing a link to the git repository containing this system. This is used by the Emulsify CLI to find components and metadata about the system.
- **structureImplementation** (REQUIRED if no `repository` field is present): Array containing objects that describe the location of each structure defined within the variant's system. Variants MUST provide a location for each structure outlined in the variant's parent system. Each object within this array MUST specify a structure `name`, and a `directory`, which will tell Emulsify where the files and components associated with the structure should live.
- **components** (REQUIRED): Array containing an object for each component available within the variant. Each object MUST specify a `category` corresponding with one of the variant's categories, SHOULD provide a `description`, and MUST have a `name`. The name of the component MUST be the same as the name of the folder containing the component. In the example `variant.emulsify.json` below, the component with the category `molecules` and the name `card` MUST have its files in `./components/02-molecules/card/`.
- **globalDirectories** (REQUIRED): Array containing objects that describe a directory or file that MUST be exported. The objects within the array MUST have a name and path and SHOULD specify a description. Files that ship with the system must be stored in the folders specified within this array.
- **requiredComponents** (OPTIONAL): Array that can contain names of components that MUST be included when the variant is installed within a project.

#### `variant.emulsify.json` or variant object

```json
{
  "system": "cornflake",
  "platform": "drupal9",
  "structureImplementations": [
    {
      "name": "base",
      "directory": "./components/00-base"
    },
    {
      "name": "atoms",
      "directory": "./components/01-atoms"
    },
    {
      "name": "molecules",
      "directory": "./components/02-molecules"
    },
    {
      "name": "organisms",
      "directory": "./components/03-organisms"
    },
    {
      "name": "pages",
      "directory": "./components/04-pages"
    }
  ],
  "components": [
    {
      "name": "card",
      "structure": "molecules",
      "description": "A card that can be used to create small sections"
    },
    {
      "name": "link",
      "structure": "atoms",
      "description": "Component that, when clicked, takes the user to a specified destination"
    }
  ],
  "requiredComponents": ["link"],
  "globalDirectories": [
    {
      "name": "asset",
      "path": "./assets",
      "description": "Contains shared assets required by components within the system"
    },
    {
      "name": "script",
      "path": "./js",
      "description": "Contains JavaScript that can be loaded in order to facilitate interactive behaviors within components."
    }
  ]
}
```

### Installation and Usage

When installing an Emulsify system, a variant MUST be selected. It will then be loaded up, scaffolded, and all the required components and assets will be added to the project in which the system is being installed.

Variants may supply components that are not required, and those can be installed within a project easily.

```bash
# In an Emulsify project with the cornflake system and the drupal9 variant:

emulsify component install card
Downloading the "card" component...
Moving the component into the "molecules" category...
Finished!

```

A custom destination can be provided. This is useful if a developer wants to install the same component multiple times, and modify the component in different cases, for example.

```bash
# In an Emulsify project with the cornflake system and the drupal9 variant:

emulsify component install card -d ./components/custom/card-2
Downloading the "card" component...
Moving the component into "./components/custom/card-2"...
Finished!
```

If a specified component does not exist within the variant, the user will be given an error message, as well as a list of the available components:

```bash
# In an Emulsify project with the cornflake system

emulsify component install foobar
  The "foobar" component does not exist within the drupal9 variant of cornflake. Would you like to select a different component?
    > card
    > link  <-
```

If a component is already installed, the user will be given a message, and will be asked to specify a destination.

```bash
# In an Emulsify project with the cornflake system

emulsify component install card
  A molecule named "card" is already present. Please run 'emulsify component install card -d "./custom/path"'
```

Components can also be listed so a user can browse through them. Once a component is selected, a sub-menu will open allowing the user to install the component, or open it within their default browser.

```bash
# In an Emulsify project with the cornflake system and the drupal9 variant:

emulsify component list
  > card
  > link  <-
```

## TODO

- Emulsify project initialization?
