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




### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
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
