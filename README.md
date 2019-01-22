[![Gitter Chat](https://badges.gitter.im/earth-reverse-engineering/lobby.svg)](https://gitter.im/earth-reverse-engineering/lobby)

![example](example.jpg "example")

#### Earth to OBJ file downloader
We can dump a textured 3D model (*.obj with *.bmp and *.jpg) using these scripts. They require [Node.js](https://nodejs.org/en/) v8 with [npm](https://www.npmjs.com/):
```sh
# Install dependencies (tested with node@8.15.0, npm@6.4.1)
npm install

# Usage:
# node dump_obj.js [octant] [max_level] [[--dump-json]] [[--dump-raw]] [[--parallel-search]]
# node lat_long_to_octant.js [latitude] [longitude]

# Find octant of latitude and longitude
node lat_long_to_octant.js 37.420806884765625 -122.08419799804688

# Dump textured obj (octant from example.jpg) to ./downloaded_files/obj directory
node dump_obj.js 20527061605273514 20
```
How to open with Blender: [BLENDER.md](BLENDER.md)

Alternative method for finding octant:
![find octant](how_to_find_octant.jpg "open maps and dev tools, switch to satellite, fly to destination, search for NodeData, copy octant path from recent request")

#### Notes for developers:

You can use this to dump json and raw data instead of obj:
```
node dump_obj.js 20527061605273514 20 --dump-json --dump-raw
```

URL structure:
```
"https://kh.google.com/rt/🅐/🅑"
 - 🅐: planet
       - "earth"
       - "mars"
       - ...
 - 🅑: resource
       - "PlanetoidMetadata"
       - "BulkMetadata/pb=!1m2!1s❶!2u❷"
          - ❶: octant path
          - ❷: epoch
       - "NodeData/pb=!1m2!1s❸!2u❹!2e❺(!3u❻)!4b0"
          - ❸: octant path
          - ❹: epoch
          - ❺: texture format
          - ❻: imagery epoch (sometimes)
```

Misc:
```
General info:
 - Everything is stored in an octree.

Roles of resources:
 - PlanetoidMetadata points to first BulkMetaData.
 - BulkMetaData points to other BulkMetaData and to NodeData.
 - NodeData contains actual meshes and textures.

Versioning:
 - BulkMetaData and NodeData are versioned using epoch numbers.
 - PlanetoidMetadata provides epoch of first BulkMetaData.
 - BulkMetaData provides epochs of underlying resources.
 - Current version of a resource can be determined recursively.
 
NodeData:
 - Vertex Buffer:
    - 8 bytes per point: X,Y,Z,W,U,U,V,V
    - XYZ: position, W: octant mask, UV: texture coordinates
 - Texture:
    - Format 1: JPEG
    - Format 6: S3 DXT1 RGB
```

Related ideas: [Racing game](https://www.reddit.com/r/Showerthoughts/comments/aex25s/race_car_video_games_could_be_amazing_if_they/) , [Minimal client](https://github.com/kaylathedev/google-maps-3d-client). WebGL + CORS should work ([test](https://retroplasma.github.io/get_planetoid_metadata.html)).

#### Important
THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
