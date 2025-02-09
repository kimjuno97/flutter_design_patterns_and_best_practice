# Response UIs for All Devices

In this chapter, we’re going to cover the following main topics:

- Understanding the Flutter layout algorithm
- Designing responsive apps with Flutter
- Ensuring accessibility in Flutter apps

## Understanding the Flutter layout algorithm

In Flutter, the layout phase is an essential part of the widget-building process.
It is during this phase that the engine calculates how the widgets are positioned and sized on the screen. The layout phase starts after the build process that we discussed in Chapter 1 is done and is followed by the painting and composition steps of the rendering pipeline.

## How do constraints determine the child widget’s size?

https://docs.flutter.dev/ui/layout/constraints

This easy and catchy rule describes the three key steps that are needed to position and lay out any widget.

Let’s break it down in more detail:

1. Constraints go down: Widget은 부모로부터 자체 제약 조건을 얻습니다. 이 위젯은 또한 자식들에게 새로운 제약 조건을 하나씩 전달합니다(이는 자식마다 다를 수 있습니다). 이 과정은 마지막 자식에게 도달할 때까지 전체 렌더 트리를 따라 걸어갑니다. 모든 렌더오브젝트에 제약 조건이 있으면 다음 단계는 실제 크기를 정의하는 것입니다.

2. Sizes go up: 사이즈가 올라갑니다: 아이는 부모가 받은 제약 조건을 충족하는 사이즈를 선택합니다. 사이즈는 알고리즘은 각 RenderObject에 의해 각자의 필요에 따라 정의됩니다. 이 단계에서는, 3단계와 함께 프레임워크는 정의된 지오메트리를 지나 렌더링 트리로 다시 이동합니다.

3. Parent sets the position: 모든 children이 자신의 크기를 정의한 후 parent는 자신의 위치를 설정합니다 children(수평으로 x축, 세로로 y축)이 하나씩 하나씩 있습니다. 모든 children이 한 번씩 위치가 정해지면 parent는 RenderObject 루트에 도달할 때까지 다시 2단계로 이동합니다.

## Understanding the limitations of the layout rule

While the layout algorithm has quite a lot of benefits, such as only needing one pass through the whole tree, it also comes with some limitations. It is important to be aware of the limitations so that we don’t try to fight against them. Let’s see what some of them are:

- A widget might not have its desired size: A child is forced to follow its parent’s constraints, yet the child’s desired size might not fit in those constraints, so the child’s final size might not match the desired one. One scenario where this can create conflicts is when the parent forces its child to fit a tight size. In our previous experiments with the Container widget, it has always been a child of the Scaffold. The Scaffold imposes its own constraints, which is why the results we have seen have corresponded with our expectations. However, there are use cases that might surprise you. In the following code, we set a container as the root widget, with a desired size of 10x10:

ex)

```dart
void main() {
  runApp(
    Container(
      height: 10,
      width: 10,
      color: Colors.red,
    ),
  );
}
```

- 위젯은 화면상의 자신의 위치를 알 수 없습니다: 또 다른 흥미로운 제약사항은 위젯 자체에서는 자신의 위치를 알 수 없다는 것입니다. 이 정보는 부모 위젯이 가지고 있습니다. 이것이 큰 단점처럼 보일 수 있지만, 자식 위젯의 크기를 항목을 배치할 때마다 다시 계산할 필요가 없는 알고리즘(예: GridView)에서는 큰 성능상의 이점을 제공합니다.

- 최종 크기와 위치는 부모를 기준으로 합니다: 마지막으로 고려할 점은 위젯의 크기와 위치가 부모 위젯에 의존한다는 것입니다. 이 부모 위젯 역시 자신의 부모에 의존합니다. 전체 위젯 트리를 고려하지 않고서는 특정 위젯의 크기와 위치를 정확하게 정의하는 것이 불가능합니다.

Now that we know the general rule and its limitations, let’s learn about the layout solutions that implement these basic concepts. We will learn how we can use them to create our own layouts. When in doubt, always remember: constraints go down, sizes go up, parent sets the position.

## Building flexible layouts

While the Stack widget arranges widgets on top of each other, you might want to lay down multiple widgets in just one dimension. In the flexible layout model, the children of a Flex widget can be laid out in a single direction and can flex their sizes, either growing to fill unused space or shrinking to avoid overflowing the parent.

## The secret of slivers

Slivers are a special type of widget that are used in conjunction with scrollable containers to enable more complex scrolling behaviors.
Slivers essentially comprise a type of flexible space that can adapt itself as the user scrolls.
You can add slivers into a CustomScrollView to create scrollable areas that contain various types of widgets, such as lists, grids, and cards. You can also define custom scrolling behaviors and animations.

Some of the most commonly used sliver widgets include the following:

- SliverAppBar: This is a sliver widget that provides an app bar that can expand and contract as the user scrolls. The sliver app bar can also float at the top of the screen and display a flexible space.
- SliverList: This is a sliver widget that displays a list of items. The SliverList is designed to work efficiently with large lists and can be used to build scrolling lists that are optimized for performance.
- SliverGrid: This is a sliver widget that displays a grid of items. The SliverGrid can be used to create scrolling grids that can be customized with various properties, such as the number of columns and the size of each grid item.
- SliverToBoxAdapter: This is a sliver widget that allows you to insert a non-scrollable widget into a CustomScrollView. It is important to note that all children of the CustomScrollView must be slivers. If you try to insert a regular widget instead of a sliver, such as a simple Container, you will get an error that says A RenderViewport expected a child of type RenderSliver but received a child of type RenderConstrainedBox. To avoid that, wrap any widget that is not a sliver and can’t be represented with a sliver analog in a SliverToBoxAdapter.
