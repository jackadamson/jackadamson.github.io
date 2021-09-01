---
title: Mkdocs
description: Useful snippets
tags:
- cheatsheet
- markdown
- polished
---
This blog is made with a python library called `mkdocs`, or more specifically using `mkdocs-material` which is a
fantastic theme that can render markdown files into a pretty website.


## Resources

- [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/) - The theme being used
- [PyMdown Extensions](https://facelessuser.github.io/pymdown-extensions/) - Tons of fancy stuff like banners, emojis and much much more

## Abbreviations

Define abbreviations to have them automatically annotated throughout the document

!!! example
    The HTML specification is maintained by the W3C.
    *[HTML]: Hyper Text Markup Language
    *[W3C]: World Wide Web Consortium
    ```
    The HTML specification is maintained by the W3C.
    *[HTML]: Hyper Text Markup Language
    *[W3C]: World Wide Web Consortium
    ```

[Abbreviations Docs](https://squidfunk.github.io/mkdocs-material/reference/abbreviations/)

## Footnotes

Create footnotes inline and have them rendered at the bottom of the page

!!! example
    See example[^1]
    [^1]: And back to where you were

    ```
    See example[^1]
    [^1]: And back to where you were
    ```

[Footnotes Docs](https://squidfunk.github.io/mkdocs-material/reference/footnotes/)

## Admonitions

Attention grabbing banners for easy reading

???+ example "Collapsible"
    Starts expanded, but can be collapsed
    ````markdown
    ???+ example "Collapsible"
        Your note here
    ````

??? example "Expandable - Expand Me!"
    Great for spoilers
    ````markdown
    ??? example "Expandable - Expand Me!"
        Your note here
    ````

!!! warning
    This is a warning
    ````markdown
    !!! warning
        Your note here
    ````
    Putting a string wrapped in `""` after the level (eg. `warning`) changes the title

There's way more than just `example` and `warning` so make sure to check out:

[Admonition Docs](https://squidfunk.github.io/mkdocs-material/reference/admonitions/#supported-types)

## Emojis

Add a splash of colour

!!! example
    :smile:
    :material-account-circle:
    :fontawesome-regular-laugh-wink:
    :fontawesome-brands-twitter:
    ```
    :smile:
    :material-account-circle:
    :fontawesome-regular-laugh-wink:
    :fontawesome-brands-twitter:
    ```

[Icons + Emojis Docs](https://squidfunk.github.io/mkdocs-material/reference/icons-emojis/)

## Keyboard Keys

Unambiguously show keyboard combinations

!!! example
    ++ctrl+alt+del++
    ```
    ++ctrl+alt+del++
    ```

[Keys Docs](https://facelessuser.github.io/pymdown-extensions/extensions/keys/)

## Task Lists

Show a checklist of tasks that need doing

- [x] Lorem ipsum dolor sit amet, consectetur adipiscing elit
- [ ] Vestibulum convallis sit amet nisi a tincidunt
    * [x] In hac habitasse platea dictumst
    * [x] In scelerisque nibh non dolor mollis congue sed et metus
    * [ ] Praesent sed risus massa
- [ ] Aenean pretium efficitur erat, donec pharetra, ligula non scelerisque

[Tasklist Docs](https://facelessuser.github.io/pymdown-extensions/extensions/tasklist/)

Another related topic is [Progress Bars](https://facelessuser.github.io/pymdown-extensions/extensions/progressbar/)

## Code Blocks

Standard syntax lighting code blocks, with a copy to clipboard button

````
```python
print("Hello!")
```
````

**Result** 
```python
print("Hello!")
```

### Line Numbers

````
``` python linenums="1"
````

``` python linenums="1"
def bubble_sort(items):
    for i in range(len(items)):
        for j in range(len(items) - 1 - i):
            if items[j] > items[j + 1]:
                items[j], items[j + 1] = items[j + 1], items[j]
```

### Highlighting Lines

````
``` python hl_lines="2 3"
````

``` python hl_lines="2 3"
def bubble_sort(items):
    for i in range(len(items)):
        for j in range(len(items) - 1 - i):
            if items[j] > items[j + 1]:
                items[j], items[j + 1] = items[j + 1], items[j]
```

### Code Block in Admonition

Nest to your heart's content

!!! example
    ```python
    the_level.pull()
    ```

For all information about code-blocks, nesting etc see:  
[Superfences Docs](https://facelessuser.github.io/pymdown-extensions/extensions/superfences/)

## Content Tabs

Have multiple tabs of content for easy organisation

=== "C"

    ``` c
    #include <stdio.h>

    int main(void) {
      printf("Hello world!\n");
      return 0;
    }
    ```

=== "C++"

    ``` c++
    #include <iostream>

    int main(void) {
      std::cout << "Hello world!" << std::endl;
      return 0;
    }
    ```

[Tabs Docs](https://facelessuser.github.io/pymdown-extensions/extensions/tabbed/)


## Graphs


!!! note
    Some of the graph lines are hard to see in dark theme, you can change themes with the button next to the search bar

Sometimes you want to visually show something, and you don't want to leave your editor. 

Or maybe you just want design decisions to go through a code-review

!!! example "Graph"
    
    === "Graph"
        ```mermaid
        graph TD
        A[Client] --> B[Load Balancer]
        B --> C[Server01]
        B --> D[Server02]
        ```

    === "Markdown"
        ````
        ```mermaid
        graph TD
        A[Client] --> B[Load Balancer]
        B --> C[Server01]
        B --> D[Server02]
        ```
        ````

!!! example "Sequence Diagram"

    === "Graph"
        ```mermaid
        sequenceDiagram
        Alice->>+John: Hello John, how are you?
        Alice->>+John: John, can you hear me?
        John-->>-Alice: Hi Alice, I can hear you!
        John-->>-Alice: I feel great!
        ```

    === "Markdown"
        ````
        ```mermaid
        sequenceDiagram
        Alice->>+John: Hello John, how are you?
        Alice->>+John: John, can you hear me?
        John-->>-Alice: Hi Alice, I can hear you!
        John-->>-Alice: I feel great!
        ```
        ````

!!! example "Gantt Diagram"

    === "Graph"
        ```mermaid
        gantt
            title A Gantt Diagram
            dateFormat  YYYY-MM-DD
            section Section
            A task           :a1, 2014-01-01, 30d
            Another task     :after a1  , 20d
            section Another
            Task in sec      :2014-01-12  , 12d
            another task      : 24d
        ```

    === "Markdown"
        ````
        ```mermaid
        gantt
            title A Gantt Diagram
            dateFormat  YYYY-MM-DD
            section Section
            A task           :a1, 2014-01-01, 30d
            Another task     :after a1  , 20d
            section Another
            Task in sec      :2014-01-12  , 12d
            another task      : 24d
        ```
        ````

[Mermaid Charts Docs](https://squidfunk.github.io/mkdocs-material/reference/diagrams/#using-flowcharts)
