#### 嵌套

&

#### SassScript

$ （变量

@if

```
$type：monster
p {
  @if $type == ocean {
    color: blue
  } @else if $type == matador {
    color: red
  } @else if $type == monster{
    color: green
  } @else {
    color: black
  }
}
```

@for

```
@for $i from 1 through 2 {
  .item-#{$i} { width: 2em * $i }
}

// 编译为
.item-1{
  width: 2em
}
.item-2{
  width: 4em
}
```

@each

```
@each $animal in cat, dog, bird {
  .#{$animal}-icon {
    color:red
  }
}

// 编译为
.cat-icon {
  color: red
}
.dog-icon{
  color: red
}
.bird-icon{
  color: red
}
```

@while 

```
$i: 6
@while $i > 0 {
  .item-#{$i} {
    width: 2em * $i
  }
  $i: $i - 2
}
```

@mixin  @include

```
@mixin is-color {
  color: red
}
.page{
  @include is-color
}

@mixin color($text, $background, $border) {
  color: $text;
  background-color: $background;
  border-color: $border;
}
.pages{
  @include('text', red, 'red')
}
```

@function

```
$grid-width: 40px
$gutter-width: 10px

@function grid-width($n) {
  @return $n * $grid-width + ($n - 1) * $gutter-width;
}
```

