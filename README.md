# three-pathfinding

[![Latest NPM release](https://img.shields.io/npm/v/three-pathfinding.svg)](https://www.npmjs.com/package/three-pathfinding)
[![Minzipped size](https://badgen.net/bundlephobia/minzip/three-pathfinding)](https://bundlephobia.com/result?p=three-pathfinding)
[![License](https://img.shields.io/badge/license-MIT-007ec6.svg)](https://github.com/donmccurdy/three-pathfinding/blob/master/LICENSE)
[![Build Status](https://github.com/donmccurdy/three-pathfinding/workflows/build/badge.svg?branch=main&event=push)](https://github.com/donmccurdy/three-pathfinding/actions?query=workflow%3Abuild)

Navigation mesh toolkit for ThreeJS, based on [PatrolJS](https://github.com/nickjanssen/PatrolJS). Computes paths between points on a 3D nav mesh, supports multiple zones, and clamps movement vectors for FPS controls. To learn how to create a navigation mesh using Blender, see [Creating a Nav Mesh](https://www.donmccurdy.com/2017/08/20/creating-a-nav-mesh-for-a-webvr-scene/).

Thanks to [Nick Janssen](https://github.com/nickjanssen) for creating [PatrolJS](https://github.com/nickjanssen/PatrolJS), which was the basis for this library.

![screenshot](https://user-images.githubusercontent.com/1848368/34424850-d79e5a24-ebf4-11e7-87c4-afc75cdc41bd.png)

## Introduction

Traditionally games and 3D apps used waypoints to help their AI agents navigate. This is bad and has a lot of problems, but is generally easier to implement than navigation meshes. Navmeshes are far more accurate, faster, and take into account the size of the AI agent (e.g. tanks require move space to maneuver than soldiers).

For a thorough introduction to Navigation mesh pathfinding, see AI Blog's article, [Fixing Pathfinding Once and For All](https://web.archive.org/web/20110807104022/http://www.ai-blog.net:80/archives/000152.html).

## Quickstart

### Installation

```
npm install --save three-pathfinding
```

### Creating a Navigation Mesh

This library does not build navigation meshes for you — instead, create a navigation mesh using [Blender](https://youtu.be/v4d_6ZCGlAg?t=6m8s), [Recast](https://github.com/recastnavigation/recastnavigation) ([CLI](https://github.com/but0n/recastCLI.js)), or another tool.

Currently, this library does not accept the custom navigation mesh file formats created by tools like Recast.
Instead, you will need to export the navigation mesh to a 3D model format (like OBJ or glTF) and then load it
with one of the three.js loaders, like THREE.OBJLoader or THREE.GLTFLoader. The library accepts a [THREE.BufferGeometry](https://threejs.org/docs/#api/core/BufferGeometry) instance, and follows the +Y=Up convention of three.js and glTF.

### Example

Loading a mesh from a `.gltf` file:

```js
// For ES6, see: https://github.com/mrdoob/three.js/issues/9562
// CommonJS
const THREE = window.THREE = require('three');
require('three/examples/js/loaders/GLTFLoader.js');

let navmesh;

const loader = new THREE.GLTFLoader();
loader.load( 'navmesh.gltf', ({scene}) => {
    scene.traverse((node) => {
        if (node.isMesh) navmesh = node;
    });
}, undefined, (e) => {
    console.error(e);
});
```

Initializing the library, creating a level, and finding a path:

```js
// ES6
import { Pathfinding } from 'three-pathfinding';
// CommonJS
const { Pathfinding } = require('three-pathfinding');
// UMD
const Pathfinding = window.threePathfinding.Pathfinding;

// Create level.
const pathfinding = new Pathfinding();
const ZONE = 'level1';
pathfinding.setZoneData(ZONE, Pathfinding.createZone(navmesh.geometry));

// Find path from A to B.
const groupID = pathfinding.getGroup(ZONE, a);
const path = pathfinding.findPath(a, b, ZONE, groupID);
```

The origin of an agent should initially be placed on the surface of the nav mesh. If needed, a dummy object can be used for pathfinding logic, and the rendered model for that agent may be placed at on offset as needed.

### Running the demo locally

```
git clone https://github.com/donmccurdy/three-pathfinding.git
cd three-pathfinding

npm install
npm run dev
```

The demo will start at http://localhost:5000/.

## API

<!--- API BEGIN --->

<!-- Generated by documentation.js. Update this documentation by updating the source code. -->

### Table of Contents

-   [Pathfinding][1]
    -   [setZoneData][2]
    -   [getRandomNode][4]
    -   [getClosestNode][6]
    -   [findPath][8]
    -   [getGroup][10]
    -   [clampStep][12]
    -   [createZone][14]
-   [PathfindingHelper][16]
    -   [setPath][17]
    -   [setPlayerPosition][19]
    -   [setTargetPosition][21]
    -   [setNodePosition][23]
    -   [setStepPosition][25]
    -   [reset][27]
-   [Zone][28]
-   [Group][30]
-   [Node][31]

## Pathfinding

Defines an instance of the pathfinding module, with one or more zones.

### setZoneData

Sets data for the given zone.

#### Parameters

-   `zoneID` **[string][33]**
-   `zone` **[Zone][34]**

### getRandomNode

Returns a random node within a given range of a given position.

#### Parameters

-   `zoneID` **[string][33]**
-   `groupID` **[number][35]**
-   `nearPosition` **Vector3**
-   `nearRange` **[number][35]**

Returns **[Node][36]**

### getClosestNode

Returns the closest node to the target position.

#### Parameters

-   `position` **Vector3**
-   `zoneID` **[string][33]**
-   `groupID` **[number][35]**
-   `checkPolygon` **[boolean][37]**  (optional, default `false`)

Returns **[Node][36]**

### findPath

Returns a path between given start and end points. If a complete path
cannot be found, will return the nearest endpoint available.

#### Parameters

-   `startPosition` **Vector3** Start position.
-   `targetPosition` **Vector3** Destination.
-   `zoneID` **[string][33]** ID of current zone.
-   `groupID` **[number][35]** Current group ID.

Returns **[Array][38]&lt;Vector3>** Array of points defining the path.

### getGroup

Returns closest node group ID for given position.

#### Parameters

-   `zoneID` **[string][33]**
-   `position` **Vector3**

Returns **[number][35]**

### clampStep

Clamps a step along the navmesh, given start and desired endpoint. May be
used to constrain first-person / WASD controls.

#### Parameters

-   `start` **Vector3**
-   `end` **Vector3** Desired endpoint.
-   `node` **[Node][36]**
-   `zoneID` **[string][33]**
-   `groupID` **[number][35]**
-   `endTarget` **Vector3** Updated endpoint.

Returns **[Node][36]** Updated node.

### createZone

(Static) Builds a zone/node set from navigation mesh geometry.

#### Parameters

-   `geometry` **BufferGeometry**
-   `tolerance` **[number][35]** Vertex welding tolerance. (optional, default `1e-4`)

Returns **[Zone][34]**

## PathfindingHelper

**Extends Object3D**

Helper for debugging pathfinding behavior.

### setPath

#### Parameters

-   `path` **[Array][38]&lt;Vector3>**

Returns **this**

### setPlayerPosition

#### Parameters

-   `position` **Vector3**

Returns **this**

### setTargetPosition

#### Parameters

-   `position` **Vector3**

Returns **this**

### setNodePosition

#### Parameters

-   `position` **Vector3**

Returns **this**

### setStepPosition

#### Parameters

-   `position` **Vector3**

Returns **this**

### reset

Hides all markers.

Returns **this**

## Zone

Defines a zone of interconnected groups on a navigation mesh.

Type: [Object][39]

### Properties

-   `groups` **[Array][38]&lt;[Group][40]>**
-   `vertices` **[Array][38]&lt;Vector3>**

## Group

Defines a group within a navigation mesh.

Type: [Object][39]

## Node

Defines a node (or polygon) within a group.

Type: [Object][39]

### Properties

-   `id` **[number][35]**
-   `neighbours` **[Array][38]&lt;[number][35]>** IDs of neighboring nodes.
-   `vertexIds` **[Array][38]&lt;[number][35]>**
-   `centroid` **Vector3**
-   `portals` **[Array][38]&lt;[Array][38]&lt;[number][35]>>** Array of portals, each defined by two vertex IDs.
-   `closed` **[boolean][37]**
-   `cost` **[number][35]**

[1]: #pathfinding

[2]: #setzonedata

[3]: #parameters

[4]: #getrandomnode

[5]: #parameters-1

[6]: #getclosestnode

[7]: #parameters-2

[8]: #findpath

[9]: #parameters-3

[10]: #getgroup

[11]: #parameters-4

[12]: #clampstep

[13]: #parameters-5

[14]: #createzone

[15]: #parameters-6

[16]: #pathfindinghelper

[17]: #setpath

[18]: #parameters-7

[19]: #setplayerposition

[20]: #parameters-8

[21]: #settargetposition

[22]: #parameters-9

[23]: #setnodeposition

[24]: #parameters-10

[25]: #setstepposition

[26]: #parameters-11

[27]: #reset

[28]: #zone

[29]: #properties

[30]: #group

[31]: #node

[32]: #properties-1

[33]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String

[34]: #zone

[35]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number

[36]: #node

[37]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Boolean

[38]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Array

[39]: https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Object

[40]: #group
<!--- API END --->

## Thanks to

* [PatrolJS](https://github.com/nickjanssen/PatrolJS)
* [bgrin's astar library](https://github.com/bgrins/javascript-astar)
* [Digesting Duck's Simple Stupid Funnel Algorithm](http://digestingduck.blogspot.jp/2010/03/simple-stupid-funnel-algorithm.html)
* [Recastnavigation's level mesh](https://github.com/memononen/recastnavigation)
* [Constrained Movement Along Navmesh pt. 3](http://digestingduck.blogspot.com/2010/07/constrained-movement-along-navmesh-pt-3.html?m=1)
