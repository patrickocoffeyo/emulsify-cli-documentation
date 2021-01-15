# Emulsify CLI

This repository contains markdown files that describe in detail the feature requirements and general purpose for the Emulsify CLI in it's 1.0 state. It also contains documentation around the organization patterns that the CLI will establish and work with.

## Term definitions (specification)

For more information on the keywords used in this documentation, please reference [RFC-2119](https://www.ietf.org/rfc/rfc2119.txt).

- **Component**: Collection of files that, when loaded into the correct system, construct a modular design element that can be used within a user interface.
- **Design System**: Collection of components that can be used to construct coherent user interfaces in a modular way.
- **Emulsify**: Tool for creating design systems with re-useable components and clear organization guidelines.
- **Emulsify System**: Set of components and organization patterns that can be pulled into Emulsify. For example, a Bootstrap Emulsify System would contain a set of basic Bootstrap components, along with an organization structure that is compatible with the Bootstrap system.
- **Emulsify CLI**: Command line interface that allows for Emulsify projects to be initialized, and gives developers tools to pull in external Emulsify systems and components.
- **MUST**: indicates that something is a non-negotiable requirement.
- **REQUIRED**: synonym of **MUST**.
- **MUST NOT**: inverse of **MUST**, indicating that the absence of something is a non-negotiable requirement.
- **SHOULD**: indicates that something is recommended, but not required.
- **SHOULD NOT**: inverse of **SHOULD**, indicating that something is not recommended, but tolerable.
- **CAN**: indicates that something is neither required, nor recommended.
- **OPTIONAL**: synonym of **CAN**.
- **IN QUESTION**: indicates that something is in question, and needs to be re-visited.

## Systems

Emulsify systems have a few primary purposes:

- Provide an organization pattern for components, styles, and other assets needed to construct user interfaces.
- Expose concise configuration that allows for agnostic tools to interact with the system's file structure. This allows for tools like the Emulsify CLI to work seamlessly with different systems that have completely different organization.

### Publishing an Emulsify System

Emulsify systems should be published to npm with the following requirements:

- MUST have an `system.emulsify.json` file with the appropriate configuration. (see the **configuration** section below).
- SHOULD specify the string `"emulsifySystem"` within the `package.json` `"keyword"` array. The camel-casing of this string may seem odd, but this naming convention will ensure that packages tagged with `"emulsify"` or `"system"` can be filtered out easily.
- MUST publish all required files as part of the package. This means that you cannot rely upon an external npm package, for example, without exporting it as part of the package. No dependency installation will occur when the system is downloaded for implementation.

### Configuration

Emulsify Systems require a certain amount of configuration in order for agnostic tools to interact with the system. The following properties are used:

- **name** (REQUIRED): string containing the name of the system. Should be unique.
- **emulsifyVariants** (REQUIRED): array of strings describing the variants of Emulsify with which the system is compatible, such as `"drupal"` or `"gatsby"`. MUST contain at least one value.
- **emulsifyCliVersion** (REQUIRED): string containing the [semantic-versioning compatible](https://semver.org/) version of the Emulsify CLI that the system is compatible with. This is in place to ensure that when breaking changes are introduced to the Emulsify CLI, users can be alerted to the fact that their project requires an older version.
- **homepage** (OPTIONAL): can contain a hyperlink to external resources (such as documentation) that will assist implementing developers.
- **componentCategories** (REQUIRED): array containing objects that describe a component category. The objects within the array MUST have a name and path and SHOULD specify a description. Components that ship with the system must be stored in the folders specified in this array.
- **globalDirectories** (REQUIRED): array containing objects that describe a directory or file that MUST be exported. The objects within the array MUST have a name and path and SHOULD specify a description. Files that ship with the system must be stored in the folders specified within this array.

The following is an example of an Emulsify System (named cornflake) configuration file.

**system.emulsify.json**

```json
{
  "name": "cornflake",
  "emulsifyVariants": ["drupal", "gatsby", "next"],
  "emulsifyCliVersion": "1.0.1",
  "homepage": "https://cornflake-ds.info",
  "componentCategories": [
    {
      "name": "base",
      "path": "./components/00-base",
      "description": "Base-level components used in virtually every higher-level component"
    },
    {
      "name": "atoms",
      "path": "./components/01-atoms",
      "description": "Small components commonly used in higher-level components"
    },
    {
      "name": "molecules",
      "path": "./components/02-molecules",
      "description": "Medium-sized components used as building blocks within a larger component"
    },
    {
      "name": "organisms",
      "path": "./components/03-organisms",
      "description": "Large components that compose smaller components into a cohesive UI"
    },
    {
      "name": "pages",
      "path": "./components/04-pages",
      "description": "Entire pages composed of smaller components"
    }
  ],
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

### Installation

Emulsify systems should be installed easily, with one command.

```bash
emulsify system install @cornflake/emulsify
```

Once this command is run, the Emulsify CLI will download the system, read and validate it's configuration file, and use the configuration to generate a folder structure. It will also pull in all the components that are specified within the system.

#### Error cases:

- If you are not in an Emulsify project, the command will fail.
- If the project already contains a system (and a `system.emuslify.json` file), the command will fail.
- If the specified system does not exist as an NPM module on the registry, the command will fail.
- If the filesystem is corrupt, or the Emulsify CLI does not have permission to operate on the folders/files within the current working directory, the command will fail.

## Components

Emulsify components allow developers to publish components into packages that can be implemented within an Emulsify system or Emulsify-based project.

## Publishing an Emulsify component

Emulsify components should be published to npm with the following requirements:

- MUST have an `component.emulsify.json` file with the appropriate configuration. (see the **configuration** section below).
- SHOULD specify the string `"emulsifyComponent"` within the `package.json` `"keyword"` array. The camel-casing of this string may seem odd, but this naming convention will ensure that packages tagged with `"emulsify"` or `"component"` can be filtered out easily.
- MUST publish all required files as part of the package. This means that you cannot rely upon an external npm package, for example, without exporting it as part of the package. No dependency installation will occur when the component is downloaded.

### Configuration

Emulsify components must expose configuration that allows the Emulsify CLI to understand what files are needed, how the component can be previewed, and other information that makes the component easy to install and use.

- **name** (REQUIRED): string containing the name of the component.
- **emulsifyVariants** (REQUIRED): array of strings describing the variants of Emulsify with which the component is compatible, such as `"drupal"` or `"gatsby"`. MUST contain at least one value.
- **emulsifyCliVersion** (REQUIRED): string containing the [semantic-versioning compatible](https://semver.org/) version of the Emulsify CLI that the component is compatible with. This is in place to ensure that when breaking changes are introduced to the Emulsify CLI, users can be alerted to the fact that their project requires an older version.
- **homepage** (OPTIONAL): can contain a hyperlink to external resources, such as documentation or ideally a rendered preview of the component.
- **category** (REQUIRED): string indicating the category of the component. This will be used to determine where the component should be installed. This string can contain anything, but it should correspond with the name of a componentGroup within the system the component belongs to. If the component is independent of a larger Emulsify system, the category should be sensible, and follow the general pattern as documented on the canonical Emulsify wiki.
- **files** (REQUIRED): must contain a list of files that define the component, such as twig files, sass files, css files, and scripts. When the component is installed into a project, these files will be placed in the component's folder.
- **globalFiles** (OPTIONAL): can contain a list of objects defining global files that will be installed within the globalDirectories specified within the Emulsify project. This is used to create files that should be shared throughout the overall design system, such as svg or font files. The objects within the array MUST have a type and path property, and SHOULD specify a description. The type property can contain any value, but similar to **category**, it should correspond with the name of a globalDirectory within the system the component belongs to.

**component.emulsify.json**

```json
{
  "name": "cornflake-card",
  "emulsifyVariants": ["drupal"],
  "emulsifyCliVersion": "1.0.1",
  "homepage": "https://cornflake-ds.info/components/card",
  "category": "molecule",
  "files": ["./styles/card.scss", "./card.twig", "./_card-heading.twig"],
  "globalFiles": [
    {
      "type": "asset",
      "path": "./close.svg",
      "description": "SVG file containing the close button for a card in modal-form"
    },
    {
      "type": "script",
      "path": "./modal-behaviors.js",
      "description": "Scripting necessary to facilitate modal behaviors within the card"
    }
  ]
}
```

### Installation

Emulsify components should be installed easily, with one command.

```bash
emulsify component install @cornflake/card
```

Once this command is run, the Emulsify CLI will download the component, read and validate it's configuration file, and use the configuration to place the component within the project.

#### Conflict handling

- If the category of the component being downloaded is not specified within the `system.emulsify.json` file, the CLI will guide the user through the process of creating a new category (simple CLI Q/A). Alternatively, the user can specify a relative path to the destination.
- If the component contains globalFiles that cannot be mapped to a globalDirectory specified within the `system.emulsify.json` file, then the user will guided through the process of creating a new global directory (simple CLI Q/A). Alternatively, the user can specify a relative path to the destination for each global file.
- If a component with the same name already exists within the project, the CLI will ask the user to specify a custom destination directory. The CLI can create the directory for the user, so any path within the project will do.

#### Error cases:

- If you are not in an Emulsify project, the command will fail.
- If the specified component does not exist as an NPM module on the registry, the command will fail.
- If the filesystem is corrupt, or the Emulsify CLI does not have permission to operate on the folders/files within the current working directory, the command will fail.

## Projects

## CLI
