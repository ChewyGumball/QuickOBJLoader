# QuickOBJLoader
A fast, C++17, header only Wavefront OBJ loading library

## Features
* Separates geometry data by material name
* Provides vertex format with byte sizes and offsets
* Triangulation of polygonal faces
* Optional deduplication of vertex data
* Templated vertex element and index types
    - `float` and `double` for vertex elements
    - `uint16_t` and `uint32_t` for index types
* Single header
    ```C++
    //In *one* non header file do 
    #define QUICK_OBJ_LOADER_IMPLEMENTATION
    #include "QuickOBJLoader.h"

    //Everywhere else
    #include "QuickOBJLoader.h"
    ```

## Non Features
* Material definitions
* Polylines
* Optional vertex w coordinate
* Optional vertex colours

## Maybe Future Features
* Non interleaved data
* Missing normal calculation

## Speed

Against [TinyOBJLoader](https://github.com/syoyo/tinyobjloader) version 1.2.0-1 from vcpkg

|Model|Size|QuickOBJLoader (File)|QuickOBJLoader (Memory)|TinyOBJLoader (File)|TinyOBJLoader (Memory)|
|---|---|---|---|---|---|
|[rungholt.obj](https://casual-effects.com/g3d/data10/index.html#mesh25)|262 MB|2.72 seconds|2.58 seconds|4.98 seconds|4.62 seconds|
|[powerplant.obj](https://casual-effects.com/g3d/data10/index.html#mesh23)|780 MB|7.02 seconds|6.52 seconds|13.62 seconds|12.32 seconds|

## API
```C++
namespace QuickOBJLoader {    
    struct VertexFormat {
        //Whether the vertex properties of a mesh are interleaved or contiguous
        bool interleaved;
        //The size in byte of each vertex element
        uint32_t elementSize;
        //The number of elements in a vertex
        uint32_t elementCount;
        //The offset of the position property of a vertex
        std::optional<uint32_t> positionElementOffset;
        //The offset of the normal property of a vertex, if there are normals
        std::optional<uint32_t> normalElementOffset;
        //The offset of the texture coordinate property of a vertex, if there are texture coordinates
        std::optional<uint32_t> textureCoordinatesElementOffset;
    };

    template<typename ElementType, typename IndexType>
    struct Mesh {
        VertexFormat format;

        std::string material;
        std::vector<ElementType> vertexData;
        std::vector<IndexType> indexData;

        //Deduplicates vertex data in place
        void deduplicateVertices();
    };
    
    template<typename ElementType, typename IndexType>
    struct Result {
        std::vector<Mesh<ElementType, IndexType>> meshes;
    };

    Result<ElementType, IndexType> LoadFromString<ElementType, IndexType>(const std::string_view data);
    Result<ElementType, IndexType> LoadFromFile<ElementType, IndexType>(const std::filesystem::path& filename);
}
```