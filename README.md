[![Build Status](https://travis-ci.com/jgraph/drawio.svg?branch=master)](https://app.diagrams.net)
[Grapheditor](https://app.diagrams.net/) for Angular.

About
-----
Graph Editor Node library for Angular projects based on the draw.io project

This project has no relation to the official draw.io project and is not endorsed by the authors of draw.io.

### Library Build
```shell
git clone https://github.com/ArfanMirza/drawio.git
cd "drawio\src\main\webpack"
npm run build 
```
TGZ file created along with dist directory. TGZ file name start from **zklogic-draw.io-**
Copy the tgz & Go to Angular Project, create lib directory along src directory and past TGZ file inside
### NPM Build & Install
```shell
npm install --save ./lib/zklogic-draw.io-*.tgz
```
### NPM Install
```shell
npm install --save @zklogic/draw.io
```
### Update Angular.json
```shell
"assets": [
  .
  .
  {
    "glob": "**/*",
    "input": "./node_modules/@zklogic/draw.io/dist/mxgraph",
    "output": "./mxgraph"
  }
]

"styles": [
  .
  .
  "./node_modules/@zklogic/draw.io/dist/mxgraph/styles/grapheditor.css"
],
```

### TS - Integration
**component.html**
```shell
<div>
    .
    .
    <div>
      <div #mxgraphScriptsContainer id="mxgraph-scripts-container"></div>
      <div #container id="mxgraph-diagram-container"></div>
    </div>
</div>
```
**component.scss**
```shell
:host ::ng-deep {
    #mxgraph-diagram-container {
      .geDialog {
        padding-left: 15px !important;
        padding-right: 15px !important;
        table {
          left : unset !important;
        }
      }
    }
  }
```
**component.ts**
```shell
import { GraphEditor, GraphEditorIn, GraphEditorOut, GraphInitConfig, GraphXmlData, ActionType, GraphEditorSVG, ButtonActionType }from '@zklogic/draw.io';
...
@ViewChild('container', { static: true }) container: ElementRef<HTMLElement>;
@ViewChild('mxgraphScriptsContainer', { static: true }) mxgraphScriptsContainer: ElementRef<HTMLElement>;
graphEditor: GraphEditor = new GraphEditor();
...

ngOnInit(): void {
let xml = "<mxGraphModel dx=\"1038\" dy=\"381\" grid=\"1\" gridSize=\"10\" guides=\"1\" tooltips=\"1\" connect=\"1\" arrows=\"1\" fold=\"1\" page=\"1\" pageScale=\"1\" pageWidth=\"850\" pageHeight=\"1100\"><root></root></mxGraphModel>";

    //Div container to load Graph Editor
    this.graphEditor.initialized(this.container.nativeElement, this.mxgraphScriptsContainer.nativeElement, {
      actions: {
        subMenu: {
          save: (xml: GraphXmlData | GraphEditorSVG): Promise<GraphEditorOut> => {
            return new Promise((resolve, reject) => {
              //save data here
              resolve({
                status: "Data Saved",
                graphData: xml
              } as GraphEditorOut)
            });
          }
        }
      },
      actionsButtons: {
        'Export Library': {
          title: "Export To App Library",
          actionType: ActionType.EXPORTSVG,
          callback: this.graphEditorLibraryExportEvent,
          callbackOnError: this.graphEditorActionsErrorEvent,
          style: {
            backgroundColor: '#4d90fe',
            border: '1px solid #3079ed',
            backgroundImage: 'linear-gradient(#4d90fe 0,#4787ed 100%)',
            height: '29px',
            lineHeight: '25px'
          }
        } as ButtonActionType,
        'Import Library': {
          title: "Import From App Library",
          actionType: ActionType.OPEN,
          callback: this.graphEditorLibraryImportEvent,
          callbackOnFinish: this.graphEditorLibraryImportFinishEvent,
          style: {
            backgroundColor: '#4d90fe',
            border: '1px solid #3079ed',
            backgroundImage: 'linear-gradient(#4d90fe 0,#4787ed 100%)',
            height: '29px',
            lineHeight: '25px'
          }
        } as ButtonActionType
      },
      extraActions: {
        file: {
          exportAs: {
            'App Library': {
              actionType: ActionType.EXPORTSVG,
              callback: this.graphEditorLibraryExportEvent,
              callbackOnError: this.graphEditorActionsErrorEvent
            }
          },
          importFrom: {
            'App Library': {
              actionType: ActionType.OPEN,
              callback: this.graphEditorLibraryImportEvent,
              callbackOnFinish: this.graphEditorLibraryImportFinishEvent
            }
          }
        }
      }
    } as GraphInitConfig)
  .then(resolve => {
        console.log(resolve)
        //Fetch last saved graph data and set after initialization
        this.graphEditor.setGrapheditorData({ xml: xml } as GraphXmlData).then(resolve => {
          console.log("setGraphEditor", resolve)
        }, reject => {
          console.log("setGraphEditor", reject)
        }).catch(e => {
          console.log("setGraphEditor", e)
        });
  }, reject => {
    console.log(reject);
  })
}

graphEditorLibraryImportFinishEvent = (graphData): Promise<GraphEditorOut> => {
    return new Promise((resolve, reject) => {
      console.log('graphEditorLibraryImportFinishEvent', graphData);
      resolve({
        status: "Import App Library Implementation required",
        graphData: graphData
      })
    })
  }

  graphEditorLibraryImportEvent = (): Promise<GraphEditorIn> => {
    return new Promise((resolve, reject) => {
      this.drawioImport.showDialog((data) => {
        console.log("callback:data", data);
        resolve({
          status: (data && data.drawio_data ? "Okay" : "cancel"),
          graphData: (data && data.drawio_data ? { xml: data.drawio_data.graphXmlData.xml, name: data.legal_name } : null)
        })
      })
    })
  }

  graphEditorActionsErrorEvent = (graphData): Promise<GraphEditorOut> => {
    return new Promise((resolve, reject) => {
      console.log('graphEditorActionsErrorEvent', graphData);
      resolve({
        status: "Export App Library Implementation required",
        graphData: graphData
      })
    })
  }

  graphEditorLibraryExportEvent = (graphData: GraphEditorSVG): Promise<GraphEditorOut> => {
    return new Promise((resolve, reject) => {
      console.log("graphData", graphData);
      resolve({
        status: "TS Export App Library Implementation required",
        graphData: (graphData && graphData.xml ? { xml: graphData.xml, name: graphData.name } : null)
      })
    })
  }
```
License
-------
The source code in this repo is licensed under the Apache v2.

The JGraph provided icons and diagram templates are licensed under the [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Additional terms may also apply where the icons are originally defined by a third-party copyright holder. We have checked in all cases that the original license allows use in this project.

Additional minified JavaScript files and Java libraries are used in this project. All of the licenses are deemed compatible with the Apache 2.0, nothing is GPL or AGPL ,due dilgence is performed on all third-party code.

Scope of the Project
--------------------

The application is designed to be used largely as-is. It's possible to alter the major parts of the interface, but if you're looking for an editor with very specific editing features, the project is likely not a good base to use.

That is to say, if you wanted to create/deply a whiteboard or diagramming application where the functionality in the main canvas is as this project provides, it more likely to be a good base to use. 
The default libraries, the menus, the toolbar, the default colours, the storage location, these can all be changed.

If you are using a draw.io project/product and have issues or questions about the editor itself, the issue tracker and discussion in this GitHub project are likely a good place to look.

Running
-------
One way to run diagrams.net is to fork this project, [publish the master branch to GitHub pages](https://help.github.com/categories/github-pages-basics/) and the [pages sites](https://jgraph.github.io/drawio/src/main/webapp/index.html) will have the full editor functionality (sans the integrations).

Another way is to use [the recommended Docker project](https://github.com/jgraph/docker-drawio) or to download [draw.io Desktop](https://get.diagrams.net).

The full packaged .war of the client and servlets is built when the project is tagged and available on the [releases page](https://github.com/jgraph/draw.io/releases).

Supported Browsers
------------------
diagrams.net supports Chrome 70+, Firefox 70+, Safari 11+, Opera 50+, Native Android browser 7x+, the default browser in the current and previous major iOS versions (e.g. 11.2.x and 10.3.x) and Edge 79+.

Open-source, not open-contribution
----------------------------------

[Similar to SQLite](https://www.sqlite.org/copyright.html), diagrams.net is open
source but closed to contributions.

The level of complexity of this project means that even simple changes 
can break a _lot_ of other moving parts. The amount of testing required 
is far more than it first seems. If we were to receive a PR, we'd have 
to basically throw it away and write it how we want it to be implemented.

We are grateful for community involvement, bug reports, & feature requests. We do
not wish to come off as anything but welcoming, however, we've
made the decision to keep this project closed to contributions for 
the long term viability of the project.
@zklogic/draw.io/dist/mxgraph
