# Markdown Help

## Markdown Doc Links

For full documentation visit [mkdocs.org](https://www.mkdocs.org).

Markdown Guide Visit [markdownguide.org](https://www.markdownguide.org/basic-syntax/).

Material Mkdocs [mkdocs-material](https://squidfunk.github.io/mkdocs-material/getting-started/).

## Install mkdocs-material

``` sh
pip install mkdocs-material
```

## Commands

* `mkdocs new [dir-name]` - Create a new project.
* `mkdocs serve` - Start the live-reloading docs server.
* `mkdocs serve -a 0.0.0.0:8000` - Start the live-reloading docs server over network.
* `mkdocs build` - Build the documentation site.
* `mkdocs -h` - Print help message and exit.

## Project layout

    mkdocs.yml    # The configuration file.
    docs/
        index.md  # The documentation homepage.
        ...       # Other markdown pages, images and other files.

## Examples

### Link Example

![Image](images/2025/Screenshot%20from%202025-01-13%2009-45-40.png){: style="height:150px"}

    ![Image](images/2025/Screenshot%20from%202025-01-13%2009-45-40.png){: style="height:150px"}

[![Image](images/2025/Screenshot%20from%202025-01-13%2009-45-40.png 'tips'){: style="height:150px"} evolutionaryneuralcodinglab](https://www.evolutionaryneuralcodinglab.sites.tau.ac.il/)

    [![Image](images/2025/Screenshot%20from%202025-01-13%2009-45-40.png 'tips'){: style="height:150px"} evolutionaryneuralcodinglab](https://www.evolutionaryneuralcodinglab.sites.tau.ac.il/)

Github [URL](https://github.com/EvolutionaryNeuralCodingLab)

    Github [URL](https://github.com/EvolutionaryNeuralCodingLab)


### Table Example

    | Tables   |      Are      |  Cool |
    |----------|:-------------:|------:|
    | col 1 is |  left-aligned | $1600 |
    | col 2 is |    centered   |   $12 |
    | col 3 is | right-aligned |    $1 |

| Tables   |      Are      |  Cool |
|----------|:-------------:|------:|
| col 1 is |  left-aligned | $1600 |
| col 2 is |    centered   |   $12 |
| col 3 is | right-aligned |    $1 |

### Comment

    <!---
    These are Comments
    Line 1
    Line 2 
    --->

### List

- First item
- Second item
- Third item
  - Indented item
  - Indented item
- Fourth item

```
- First item
- Second item
- Third item
  - Indented item
  - Indented item
- Fourth item
```

### Local Link

```
[Local Page Link to ## Examples](#examples)
```
[Local Page Link to ## Examples](#examples)

### Footnote

Footnotes are a great addition to documentation.[^note] They provide extra context without interrupting the main reading flow.[^2]

[^note]: This is the first note.

[^2]: And this is another note with a slightly different identifier.

```
Footnotes are a great addition to documentation.[^note] They provide extra context without interrupting the main reading flow.[^2]

[^note]: This is the first note.

[^2]: And this is another note with a slightly different identifier.
```

### Block Quote

> This is a quote

```
> This is a quote
```

### Markdown Help

[Markdown Doc from codecademy](https://www.codecademy.com/resources/docs/markdown)

[mkdocs-material reference](https://squidfunk.github.io/mkdocs-material/reference/)

### Markdown Preview

Control-Shift-V

### WaveDROM Help

[WaveDOM Help](https://wavedrom.com/tutorial.html)

### WaveDROM Preview

Control-Shift-P

### Pay Attention 

#### Don't put large file 
