# Sprite Sorting and Camera Culling

## What is this? 

In general, sprite sorting is basically the order of your sprites to render in order to not overlap. 



![Image](https://images.squarespace-cdn.com/content/v1/60e846ddc80b76633f04495a/1628097854897-GY1HFNL83P4DNI058W4R/problem_2.gif?format=750w)

## Why is this important?
To show logical perception for the player. If there are few sprites, it can be done manually. But if there are a dozen of sprites then it is better to use automatic sorting.

![Image](https://github.com/boscobarberesbert/sprite-sorting-and-camera-culling/blob/master/docs/images/the_legend_of_zelda.gif?raw=true)

## Z-Order
Z-order is an ordering of overlapping two-dimensional objects by setting which one has higher order over the other


Theory:


![Image](https://github.com/boscobarberesbert/sprite-sorting-and-camera-culling/blob/master/docs/images/z-order.png?raw=true)

Example: 

![Image](https://user-images.githubusercontent.com/79082037/165656837-41463c03-7bda-4c4b-8671-c00a2037959f.png)
![image](https://user-images.githubusercontent.com/79082037/165656889-99e8a01b-a427-449d-ae5e-700a980352d6.png)
![image](https://user-images.githubusercontent.com/79082037/165656900-1d8175d1-8df9-4cae-acda-1262774d0e87.png)




### Example of Implementation

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
int tileCount;
struct TileProperty
{
  int tileId;
  Properties properties;
} tileProperty[MAX_TILES_WITH_PROPERTIES];
```
Then, in the .cpp, read and store the variables. Something like this:
```markdown
set->tileCount = 0;
pugi::xml_node actualNode = tileset_node.child("tile");
while (actualNode.child("properties"))
{
  set->tileProperty[set->tileCount].tileId = actualNode.attribute("id").as_int();
  LoadProperties(actualNode, set->tileProperty[set->tileCount].properties);
  
  actualNode = actualNode.next_sibling();
  set->tileCount++;
}
```

### After that
We need in the entity manager a function to draw entities and maps.
So, for the moment, adapt how the map and the entities are drawed.

When rendering each tile, detect if the tile is one with an attribute.

```markdown
for (int t = 0; t < tileset->tileCount; t++)
{
  if (tileId - 1 == tileset->tileProperty[t].tileId) 
  {}
}
```

This class will store all the information about the generated assembles, that are tiles united to form like entities.
It should have enough variables for the sorting and the render.
This are the variables I used:

```markdown
class Assemble
{
public:

  int tilesAssemble = 0;
  struct TileInfo
  {
    iPoint tileMapPosition;
    iPoint tileWorldPosition;
    TileSet* tileset;
    SDL_Rect rectangle;
    
  } tileInfo[MAX_TILES_ASSEMBLED];
```


To generate assembles we should check previous tiles in the X and Y axis.

```markdown
int tileIdPrevX = layer->Get(x - 1, y);
int tileIdPrevY = layer->Get(x, y - 1);
TileSet* tilesetPrevX = nullptr;
TileSet* tilesetPrevY = nullptr;

if (tileIdPrevX > 0)
  tilesetPrevX = map->GetTilesetFromTileId(tileIdPrevX);
if (tileIdPrevY > 0)
  tilesetPrevY = map->GetTilesetFromTileId(tileIdPrevY);
int propPrevX = -1;
int propPrevY = -1;

for (int t = 0; t < tileset->tileCount; t++)
{
  if (tileId - 1 == tileset->tileProperty[t].tileId)
  {
    if (tileIdPrevX > 0)
      for (int tp = 0; tp < tileset->tileCount; tp++)
        if (tileIdPrevX - 1 == tilesetPrevX->tileProperty[tp].tileId)
        {
        propPrevX = tilesetPrevX->tileProperty[tp].properties.GetProperty("detectAssamble", 0);
        break;
        }
    if (tileIdPrevY > 0)
      for (int tp = 0; tp < tileset->tileCount; tp++)
        if (tileIdPrevY - 1 == tilesetPrevY->tileProperty[tp].tileId)
        {
        propPrevY = tilesetPrevY->tileProperty[tp].properties.GetProperty("detectAssamble", 0);
        break;
        }
        
     if (tileset->tileProperty[t].properties.GetProperty("detectAssamble", 0) == propPrevX) 
     {}
     else if (tileset->tileProperty[t].properties.GetProperty("detectAssamble", 0) == propPrevY) 
     {}
     else
     {}
     break;
  }
}
```

For more details see [Basic writing and formatting syntax](https://docs.github.com/en/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/Oga29/SSCC/settings/pages). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Documentation

References from Github Presentations:
- [sherzock's Research](https://sherzock.github.io/Sprite-Ordering-and-Camera-Culling/) 
- [boscobarberesbert's Research](https://boscodev.com/sprite-sorting-and-camera-culling/) 
- [christt105's Research](https://christt105.github.io/Sprite_Ordering_and_Camera_Culling_Personal_Research/) 
- [Ercanon's Research](https://ercanon.github.io/Sprite-Sorting-Camera-Culling/) 

References from Other websites:
- [How to correctly sort isometric sprites](https://gamedev.stackexchange.com/questions/8151/how-do-i-sort-isometric-sprites-into-the-correct-order)
- [C++ Queue](http://www.cplusplus.com/reference/queue/priority_queue/)
- [Discussion of ordering objects](https://stackoverflow.com/questions/11002811/sorting-objects-by-y-value-before-rendering)
