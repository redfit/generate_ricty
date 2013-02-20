Ricty generator
===============
generates Ricty for *nix and Windows automatically.

Requirements
------------
* Ruby >= 1.9
* fontforge with Python support
* curl

Usage
-----
```
$ git clone https://github.com/waltarix/generate_ricty.git
$ cd generate_ricty
$ rake
$ tree products
products
|-- Ricty-Bold.ttf
|-- Ricty-Regular.ttf
|-- RictyDiscord-Bold-Powerline.ttf
|-- RictyDiscord-Bold.ttf
|-- RictyDiscord-Regular-Powerline.ttf
|-- RictyDiscord-Regular.ttf
|-- RictyDiscordAscii-Bold.ttf
|-- RictyDiscordAscii-Regular.ttf
`-- windows
    |-- Ricty-Bold.ttf
    |-- Ricty-Regular.ttf
    |-- RictyDiscord-Bold-Powerline.ttf
    |-- RictyDiscord-Bold.ttf
    |-- RictyDiscord-Regular-Powerline.ttf
    |-- RictyDiscord-Regular.ttf
    |-- RictyDiscordAscii-Bold.ttf
    `-- RictyDiscordAscii-Regular.ttf
```

Products
-----
* products/*.ttf
  * for *nix
* products/windows/*.ttf
  * for windows
