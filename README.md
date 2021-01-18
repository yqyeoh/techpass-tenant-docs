# TechPass User Doc Repository
This is a documentation repository for TechPass.

> The repository is currently in active development and  contents will change rapidly.



## File Structure
All markdown files should live within the **/docs** folder. Each main section should be a separate markdown file by itself. If you have added a new main section, you should also update [_sidebar.md](docs/_sidebar.md) so that it will show up when the webpage is rendered.

All non-markdown files (i.e. images and sample code files) should live within the assets subfolder.

## Running Locally
### Setting Up
- Install docsify cli `npm -g install docsify-cli`
- Git pull this repository
- Run `docsify serve .`

### Syntax Highlighting
The repository has been pre-configured to pull in the default Developer Portal's styling for Documentations. (See [index.html](./index.html))

## Web Hosting
By default, once you commit and push any changes into github, the contents will be updated automatically. There's no need for you to do anything.

## Contributing
Please fork the repository, make your changes and submit a Pull Request.
