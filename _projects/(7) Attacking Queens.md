---
name: Attacking Queens
tools: [ruby, rspec, httpretty, chessboard, queens, pieces]
image: https://i.ibb.co/XDr89Gy/fotor-ai-202307145022.jpg
description: A Ruby program uses an IP address latitude to build a chess-board to randomly place queens and return each pair of attacking Queens.
# external_url: https://github.com/rah00l/marvel-comics-api
---

# [Attacking Queens](https://github.com/rah00l/attacking-queens)

A Ruby program uses an IP address latitude to build a chess-board to randomly place queens and return each pair of attacking Queens.

<img src="{{ site.baseurl }}/public/images/attacking-queens2.jpg"/>

## Installation

    $ git clone https://github.com/rah00l/attacking-queens

Set rvm environment by creating gemset for this application.

And then execute:

    $ bundle

## Contents

* [Usage](#usage)
* [Responses](#responses)

## Usage

### To call the program directly instantiate a game

```
// irb
rahul@rahul-Inspiron-N5010 attacking-queens (master) $irb -I lib
2.3.3 :001 > require 'game'
 => true
2.3.3 :002 > Game.new.run
geodata loc -> 37.3860,-122.0840
 => [[[34, 16], [34, 13]], [[34, 16], [36, 14]], [[11, 20], [2, 20]], [[11, 20], [11, 12]], [[16, 3], [18, 3]], [[37, 25], [37, 35]], [[37, 25], [37, 26]], [[34, 13], [29, 8]], [[2, 20], [2, 37]], [[27, 19], [26, 19]], [[37, 35], [37, 26]], [[29, 8], [8, 29]]] 
2.3.3 :003 > Game.new('1.23.195.129').run
geodata loc -> 18.5333,73.8667
 => [[[15, 13], [4, 2]], [[16, 16], [4, 4]], [[16, 16], [6, 6]], [[17, 6], [6, 6]], [[4, 2], [4, 4]], [[4, 4], [6, 6]]]
2.3.3 :004 >
```

See the list of [available
methods](https://github.com/rah00l/attacking-queens/wiki/Documentation) in the
wiki.


## Responses

Program returns either an array or string where an array contains each pair of attacking queens coordinates.

<br/>
