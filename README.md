# Test-bed design documentation

This repository is intended as a manual for end-users of the DRIVER+ Test-bed and to document the main design decisions. It can be read online [here](https://driver-eu.github.io/test-bed-design).

In this file, the steps to locally edit the book, and the steps needed to create the PDF/ePub/Mobi ebooks, are described.

## Installation instructions

To install the Gitbook service, and the necessary plugins, run:

```console
npm i # alternative, use pnpm i
```

Additionally, in order to render the UML diagrams, you need to [install Graphviz](https://graphviz.gitlab.io/download). If you also wish to create ebooks, also install [Calibre](https://calibre-ebook.com/).

## Build instructions

To run the local Gitbook service, run `npm start`, and access the content at [localhost:4000](http://localhost:4000). 

**NOTE for Windows users:** When the gitbook server crashes upon edits, use the `start_gitbook.bat` script. It restarts the gitbook server upon a crash.

## Update public website

The project's output is hosted on [GitHub Pages](https://driver-eu.github.io/test-bed-design). When you are ready to publish an update, run `npm build:pages`. It can take a bit of time before the content is refreshed.

## Create eBooks

To create the eBooks, follow the documentation [here](https://toolchain.gitbook.com/ebook.html). You can either create them separately, using `npm run build:pdf`, `npm run build:epub` or `npm run build:mobi`, or all at once using `npm run build:ebooks`. The output will be available in the `./_book/ebooks` folder.

## Notes

### Using UML

In order to use UML ([PlantUML](http://plantuml.com) syntax), put it inside a UML codeblock, e.g.

```uml
@startuml

    Class Stage
    Class Timeout {
        +constructor:function(cfg)
        +timeout:function(ctx)
        +overdue:function(ctx)
        +stage: Stage
    }
     Stage <|-- Timeout

@enduml
```

### Publishing on gh_pages

To create the initial version of the gh_pages branch to publish the eBook on GitHub, the following [site](https://gist.github.com/ramnathv/2227408) was instructive.
