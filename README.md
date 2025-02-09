<h1 align="center">Tolkien Gateway Map</h1>
<p align="center">A web app for visualizing the connections between Tolkien gateway pages. Try it at <a href="https://map.tolkiengateway.net">map.tolkiengateway.net</a>. Based on the Wikipedia map at wikipedia.luk.ke</p>

![Screenshot of Wikipedia Map](screenshots/wikipedia-map-safari.png)


## Usage
Start by entering a topic into the text box, for example *Cats*. A single “node” will be generated, labeled *Cat*, which appears as a circle on the graph. Click this node to expand it.

Expanding a node creates a **new node for each Wikipedia article linked in the first paragraph of the article you clicked**. These new nodes will be connected to the node from which they were expanded. For example, expanding *Cat* will create eight nodes, including *Fur*, *Mammal*, *Carnivore*, and *Domestication*, each of which will be connected to *Cat*. These new nodes can also be expanded in the same way. By continuing to expand nodes, you can build a complex web of related topics.

You can also enter multiple articles to "compare" by pressing Comma, Tab, or Enter after each one you enter.


## How it works

#### API
When you click to expand a node, a request is made to the Wikipedia API to download the full content of the Wikipedia article corresponding to that node. Wikipedia map uses this data to find the links in the first paragraph of the article.

#### HTML Parsing
`wikipedia_parse.js` uses the [`DOMParser` API](https://developer.mozilla.org/en-US/docs/Web/API/DOMParser) to parse wikipedia pages’ HTML (retrieved from calls to Wikipedia's API). The parser looks for the `<p>` tag corresponding to the first paragraph of the article, then extracts all of the `<a>` tag links within this paragraph. It then filters the links to include only those which link to other wikipedia articles.

You can see this in action yourself in your browser’s console. If you have Wikipedia Map open, open your browser’s developer tools and type `await getSubPages('Cat')`. After a second, you should see an array with the names of other related articles.

#### The graph
The front-end uses [`vis.js`](https://visjs.org/) to display the graph. Every time a node is clicked, the app makes a `XMLHttpRequest` to the Node.js server. The resulting links are added as new nodes, colored according to their distance from the central node (as described above).


## Cloning
To use the app locally, simply
```bash
git clone https://github.com/controversial/wikipedia-map/
```
and open `index.html` in a web browser. No compilation or server is necessary to run the front-end.


## Design choices

#### Functional
Expanding a node creates nodes for each article linked in the _first paragraph_ of the article for the node you expand. I've chosen to use links only from the first paragraph of an article for 2 reasons:

1. There is usually a manageable number of these links, about 5-10 per page.
2. These links tend to be more directly relevant to the article than links further down in the page.

#### Visual
Nodes are lighter in color when they are farther away from the central node. If it took 5 steps to reach *Ancient Greek* from *Penguin*, it will be a lighter color than a node like *Birding*, which only took 2 steps to reach. Thus, a node's color indicates how closely an article is related to the central topic.

Hovering the mouse over a node will highlight the path back to the central node:
![Traceback](screenshots/traceback.png)
This is not necessarily the shortest path back; it is the path that you took to reach the node.


## Credits
This project is powered by Tolkien Gateway.

The presentation of the graph is powered by [`vis.js`](https://visjs.org).
