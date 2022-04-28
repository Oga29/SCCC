# Sprite Sorting and Camera Culling


# Sprite Sorting

## What is this? 

In general, sprite sorting is basically the order of your sprites to render in order to not overlap. 



![Image](https://images.squarespace-cdn.com/content/v1/60e846ddc80b76633f04495a/1628097854897-GY1HFNL83P4DNI058W4R/problem_2.gif?format=750w)

## Why is this important?
To show logical perception for the player. If there are few sprites, it can be done manually. But if there are a dozen of sprites then it is better to use automatic sorting.

![Image](https://github.com/boscobarberesbert/sprite-sorting-and-camera-culling/blob/master/docs/images/the_legend_of_zelda.gif?raw=true)

## Z-Order
Z-order is an ordering of overlapping two-dimensional objects by setting which one has higher order over the other


Theory:


![Image](https://media.discordapp.net/attachments/258661801749774336/969057785520209980/Untitled-1.png?width=567&height=567)

Example: 

![Image](https://user-images.githubusercontent.com/79082037/165656837-41463c03-7bda-4c4b-8671-c00a2037959f.png)
![image](https://user-images.githubusercontent.com/79082037/165656889-99e8a01b-a427-449d-ae5e-700a980352d6.png)
![image](https://user-images.githubusercontent.com/79082037/165656900-1d8175d1-8df9-4cae-acda-1262774d0e87.png)

## Cut Sprites
This is the laziest way to solve the sorting sprites problem, but also the one that can become a slow and cumbersome way in the long run. Although that, it can serve ample in many cases. It consists in separating a sprite in two parts, the lower part and the higher part. So, the core of the system is to render first the lower part, later all the entities, and finally the higher part. That system is good to mix static and dynamic entities, for example a building isometric game. There is an example of Pocket City made. It is quite interesting and fits well in that project for the simplicity of the project, the isometric type map and the mobile resources. You can see the separated layers and the result, tinted to see where the cut is.

![Image](https://github.com/boscobarberesbert/sprite-sorting-and-camera-culling/blob/master/docs/images/pocket_city_5.png?raw=true)
![Image](https://github.com/boscobarberesbert/sprite-sorting-and-camera-culling/blob/master/docs/images/pocket_city_2.png?raw=true)
![Image](https://github.com/boscobarberesbert/sprite-sorting-and-camera-culling/blob/master/docs/images/pocket_city_6.gif?raw=true)

## Dynamic Sorting

### By Y - Position

That consists in sorting entities depending on the position of an entity. It is only focused on the vertical position (Y). In order to make sense of depth, all entities and objects will be sorted by Y position, from the lower Y to the higher Y, from top of the window to down. Entities placed higher will be rendered before entities placed lower. 

![Image](https://user-images.githubusercontent.com/79082037/165656837-41463c03-7bda-4c4b-8671-c00a2037959f.png)
![image](https://user-images.githubusercontent.com/79082037/165656889-99e8a01b-a427-449d-ae5e-700a980352d6.png)
![image](https://user-images.githubusercontent.com/79082037/165656900-1d8175d1-8df9-4cae-acda-1262774d0e87.png)

### By Collider

It is based on putting colliders in different zones that on the one hand change the layer of the entity regarding the elements of the map, and on the other hand they activate or deactivate the colliders that define the path that players can take.

![Image](https://github.com/boscobarberesbert/sprite-sorting-and-camera-culling/blob/master/docs/images/colliders_example_2.png?raw=true)
![image](https://github.com/boscobarberesbert/sprite-sorting-and-camera-culling/blob/master/docs/images/colliders_example_3.png?raw=true)



# Camera Culling

## What is Camera Culling?

Camera Culling is a technique used by game developers to optimize their games. Normally when we create a game for the first time we try to render everything in the level, map, arena, etc even though these there is stuff outside the camera, and that the player is not able to see.

What camera culling does is that reduces what we render only to the things we can see in the camera, and the stuff that it’s not visible for the user doesn't get rendered.

## Why is this important 

As we mentioned before, Camera Culling is a way to optimize the game, by using this technique we are going to be able to increase the performance of our game as we are going to be loading less sprites and entities each cycle.


### Example of Implementation

Code implementation that was taken as an example.

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


Store information about the assembles that are going to be created. If the actual tile and the previous have the same attribute, the actual tile will count as the assemble of the previous tile. If not, it will generate a new assemble.

```markdown
if (tileset->tileProperty[t].properties.GetProperty("detectAssamble", 0) == propPrevX)
{
    for (int a = 0; a < assembledList.Count(); a++)
    {
        bool done = false;
        for (int i = 0; i < assembledList.At(a)->data->tilesAssemble; i++)
	{
	    if (assembledList.At(a)->data->tileInfo[i].tileMapPosition.x == x - 1 && 
	        assembledList.At(a)->data->tileInfo[i].tileMapPosition.y == y)
	    {
		assembledList.At(a)->data->tileInfo[assembledList.At(a)->data->tilesAssemble].tileMapPosition = { x, y };
		assembledList.At(a)->data->tileInfo[assembledList.At(a)->data->tilesAssemble].tileWorldPosition = pos;
		assembledList.At(a)->data->tileInfo[assembledList.At(a)->data->tilesAssemble].rectangle = rec;
		assembledList.At(a)->data->tileInfo[assembledList.At(a)->data->tilesAssemble].tileset = tileset;
		assembledList.At(a)->data->tilesAssemble++;
		done = true;
		break;
	    }
	}
	if (done) break;
    }
}
else if (tileset->tileProperty[t].properties.GetProperty("detectAssamble", 0) == propPrevY)
{
    for (int a = 0; a < assembledList.Count(); a++)
    {
        bool done = false;
	for (int i = 0; i < assembledList.At(a)->data->tilesAssemble; i++)
	{
	    if (assembledList.At(a)->data->tileInfo[i].tileMapPosition.x == x && 
	        assembledList.At(a)->data->tileInfo[i].tileMapPosition.y == y - 1)
	    {
	        assembledList.At(a)->data->tileInfo[assembledList.At(a)->data->tilesAssemble].tileMapPosition = { x, y };
		assembledList.At(a)->data->tileInfo[assembledList.At(a)->data->tilesAssemble].tileWorldPosition = pos;
		assembledList.At(a)->data->tileInfo[assembledList.At(a)->data->tilesAssemble].rectangle = rec;
		assembledList.At(a)->data->tileInfo[assembledList.At(a)->data->tilesAssemble].tileset = tileset;
		assembledList.At(a)->data->tilesAssemble++;
		done = true;
		break;
	    }
	}
	if (done) break;
    }
}
else
{
    assemble = new Assemble;
    assemble->tileInfo[0].tileMapPosition = { x, y };
    assemble->tileInfo[0].tileWorldPosition = pos;
    assemble->tileInfo[0].rectangle = rec;
    assemble->tileInfo[0].tileset = tileset;
    assemble->tilesAssemble++;
    assembledList.Add(assemble);
}
```

### The Sorting Starts

First, organize the entities by their position in the Y axis so, when drawing them, the entities will render in the correct order:

```markdown
ListItem<Entity*>* list = entities.start;
bool swapped = true;
while (swapped)
{
    swapped = false;
    while (list != NULL && list->next != NULL)
    {
    	if (list->data->position.y < list->next->data->position.y)
	{
	    SWAP(list->data, list->next->data);
	    swapped = true;
	}
	
	list = list->next;
    }
}
```

Create a new ListItem<Entity> to copy the previous list sorted.
  
  ```markdown
The list sorted is inverted, so when the copy is used it must be rendered backwards (x->prev)
```
To get the dimensions of the assemble, we go for each assemble created getting its dimensions.
  
   ```markdown
for (int l = 0; l < assembledList.Count(); l++)
{
    int maxX = assembledList.At(l)->data->tileInfo[0].tileWorldPosition.x;
    int minX = assembledList.At(l)->data->tileInfo[0].tileWorldPosition.x;
    int maxY = assembledList.At(l)->data->tileInfo[0].tileWorldPosition.y;
    int minY = assembledList.At(l)->data->tileInfo[0].tileWorldPosition.y;
    
    for (int a = 0; a < assembledList.At(l)->data->tilesAssemble; a++)
    {
    	if (maxX < assembledList.At(l)->data->tileInfo[a].tileWorldPosition.x + map->data.tileWidth) 
	    maxX = assembledList.At(l)->data->tileInfo[a].tileWorldPosition.x + map->data.tileWidth;
	if (minX > assembledList.At(l)->data->tileInfo[a].tileWorldPosition.x)
	    minX = assembledList.At(l)->data->tileInfo[a].tileWorldPosition.x;
	if (maxY < assembledList.At(l)->data->tileInfo[a].tileWorldPosition.y + map->data.tileHeight)
	    maxY = assembledList.At(l)->data->tileInfo[a].tileWorldPosition.y + map->data.tileHeight;
	if (minY > assembledList.At(l)->data->tileInfo[a].tileWorldPosition.y)
	    minY = assembledList.At(l)->data->tileInfo[a].tileWorldPosition.y;
    }
}
```
  
  
  Using the previous Part generate all entities that are under an assemble.
  
  
  ```markdown
sorted = list;
while (sorted != NULL)
{
   if (sorted->data->position.x + sorted->data->width >= minX && sorted->data->position.x <= maxX &&
       sorted->data->position.y + sorted->data->height >= minY && sorted->data->position.y <= maxY &&
       sorted->data->renderable == true)
   {
   	sorted->data->Draw();
	sorted->data->renderable = false;
   }
   sorted = sorted->prev;
}
```
  
  Draw every assemble created, then delete them and clear the list to avoid memory leaks.
  
  ```markdown
for (int l = 0; l < assembledList.Count(); l++)
{
    for (int a = 0; a < assembledList.At(l)->data->tilesAssemble; a++)
    {
    	render->DrawTexture(assembledList.At(l)->data->tileInfo[a].tileset->texture,
	    assembledList.At(l)->data->tileInfo[a].tileWorldPosition.x + assembledList.At(l)->data->tileInfo[a].tileset->offsetX,
	    assembledList.At(l)->data->tileInfo[a].tileWorldPosition.y + assembledList.At(l)->data->tileInfo[a].tileset->offsetY,
	    &assembledList.At(l)->data->tileInfo[a].rectangle);
    }
    delete assembledList.At(l)->data;
    assembledList.At(l)->data = nullptr;
}
assembledList.Clear();
}
```
  
  
  Draw the rest of the entities that have not been rendered.
  
 ```markdown
sorted = list;
while (sorted != NULL)
{
    if (sorted->data->renderable == true)
	    sorted->data->Draw();
	    
    sorted->data->renderable = true;
    sorted = sorted->prev;
}
```
  
  
### Camera Culling
  Implement the camera culling. If any tile is not in the camera, will not be calculated and drawed.
  
  ```markdown
for (int y = 0; y < map->data.height; ++y)
{
    for (int x = 0; x < map->data.width; ++x)
    {
        iPoint pos = map->MapToWorld(x, y);
	
	if (pos.x + map->data.tileWidth > -render->camera.x && pos.x < -render->camera.x + render->camera.w &&
	    pos.y + map->data.tileWidth > -render->camera.y && pos.y < -render->camera.y + render->camera.h)
	{}
    }
}
if (list->data->position.x + list->data->width > -render->camera.x && 
    list->data->position.x < -render->camera.x + render->camera.w &&
    list->data->position.y + list->data->height > -render->camera.y && 
    list->data->position.y < -render->camera.y + render->camera.h)
{}
```
  
  
  
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
