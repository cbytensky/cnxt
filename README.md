# cnxt

9×20 Bitmap monospaced font. “cnxt” stands for “Constantine’s Nine to Twenty”. Contains latin, greek, hebrew, cyrrilic, many symbols and line drawing chars. [gbdfed](http://sofia.nmsu.edu/~mleisher/Software/gbdfed/) was used for creating. For building [rake](https://github.com/ruby/rake) is used. 

## Rake targets

```
rake otb
```
Builds `cnxt.otb`, Open Type Bitmap font. 

```
rake pcf
```
Builds `cnxt.pcf`, PCF font for X Window System. 

```
rake psfu
```
Builds `cnxt.psfu` PC Screen Font for Linux console. File format limits number of chars to 512. This set of chars is defined by `cnxt.glyphs` file.

```
rake c
```
Builds `font_8x16.c` file, than can be put into Linux sources in `lib/fonts/` dir in order to compile font directly into Linux kernel as default font for framebuffer console.

```
rake cnxt.sorted
```
Gets `cnxt.glyphs`, removes spaces, sorts it like `a[96..127] + [' '] + a[0..95] + a[128..511]` (because space char have to be at pos. 32) and formats it.  


```
rake cnxt.neat.bdf
```
Creates new BDF by assigning character names like `U+XXXX`.

## Other files

`cnxt.bdf` - Source BDF.

`cnxt-cp1251.fon`, `cnxt-cp1125.fon` - Windows .fon files, codepages are ANSI 1251 (cyrillic) and OEM 1125 (Ukrainian). They were converted manually using [FontForge](https://fontforge.org/).

## Demo
    
![](https://github.com/cbitensky/cnxt/blob/main/cnxt-UTF-8-demo.webp)
