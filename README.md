# Emulsify CLI

This respository contains markdown files that describe in detail the feature requirements and general purpose for the EMulsify CLI in it's 1.0 state. It also contains documentation around the organization patterns that the CLI will establish and work with.

## Term definitions

- **Component**: Collection of files that, when loaded into the correct system, construct a modular design element that can be used within a user interface.
- **Design System**: Collection of components that can be used to construct coherent user interfaces in a modular way.
- **Emulsify**: Tool for creating design systems with re-useable components and clear organization guidelines.
- **Emulsify System**: Set of components and organization patterns that can be pulled into Emulsify. For example, a Bootstrap Emulsify System would contain a set of basic Bootstrap components, along with an organization structure that is compatible with the Bootstrap system.
- **Emulsify CLI**: Command line interface that allows for Emulsify projects to be initialized, and gives developers tools to pull in external Emulsify systems and components.
- **MUST**: indicates that something is a non-negotiable requirement.
- **REQUIRED**: synonym of **MUST**.
- **MUST NOT**: inverse of **MUST**, indicating that the absence of something is a non-negotiable requirement.
- **OPTIONAL**: synonym of **MUST NOT**.
- **SHOULD**: indicates that something is recommended, but not required.
- **SHOULD NOT**: inverse of **SHOULD**, indicating that something is not recommended, but tolerable.
- **CAN**: indicates that something is neither required, nor recommended.
- **IN QUESTION**: indicates that something is in question, and needs to be re-visited.

## Systems

Emulsify Systems have a few primary purposes:

- Provide an organization pattern for components, styles, and other assets needed to construct user interfaces.
- Expose concise configuration that allows for agnostic tools to interact with the system's file structure. This allows for tools like the Emulsify CLI to work seamlessly with different systems that have completely different organization.

### Publishing an Emulsify System

Emulsify Systems should be published to npm with the following requirements:

- MUST have an `emulsify.system.json` file with the appropriate configuration. (see the **configuration** section below).
- SHOULD specify the string `"emulsifySystem"` within the `package.json` `"keyword"` array. It may be odd that this string is camel-cased, but this will ensure that packages tagged with `"emulsify"` or `"system"` can be filtered out easily.
- MUST publish all required files as part of the package. This means that you cannot rely upon an external npm package, for example, without exporting it as part of the package. No dependency installation will occur when the system is downloaded for implementation.

### Configuration

Emulsify Systems require a certain amount of configuration in order for agnostic tools to interact with the system. The following properties are used:

- **name** (REQUIRED): string containing the name of the system. Should be unique.
- **emulsifyVariants** (REQUIRED): array of strings describing the variants of Emulsify with which the system is compatible, such as `"drupal"` or `"gatsby"`. MUST contain at least one value.
- **homepage** (OPTIONAL): can contain a hyperlink to external resources (such as documentation) that will assist implementing developers.
- **categories** (REQUIRED): array containing objects that describe a component category. The objects within the array MUST have a name and path and SHOULD specify a description.
- **exports** (REQUIRED, IN QUESTION): array containing objects that describe a directory or file that MUST be exported. The objects within the array MUST have a name and path and SHOULD specify a description.

The following is an example of an Emulsify System (named cornflake) configuration file.

**project.emulsify.json**

```json
{
  "name": "cornflake",
  "emulsifyVariants": ["drupal", "gatsby", "next"],
  "homepage": "https://cornflake-ds.info",
  "categories": [
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
  "exports": [
    {
      "name": "assets",
      "directory": "./assets",
      "description": "Contains shared assets required by components within the system"
    },
    {
      "name": "styles",
      "directory": "./styles",
      "description": "Contains shared styles required by components within the system"
    },
    {
      "name": "mainStyles",
      "directory": "./main.scss",
      "description": "Main stylesheet containing all styles"
    }
  ]
}
```

## Components

## Projects

## CLI
