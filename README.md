# Team Picker
Input team stats (you may need to adjust the input parser) and get an ideal team output. Handles offence/defence positions separately. Includes default picker for BrutalBall.

## To use:

Download from here: https://github.com/arkadye/team_picker/releases

OR

Get yourself a C++ compiler. This was written with Visual Studio Community Edition 2022 so I know for sure that works.

Compile main.cpp.

Setup your composition.txt to describe the positions being picked and the calculations to base each position on.

Put your team, with their stats, into team_data.txt.

Make sure team_data.txt and composition.txt are in the current working directory.

Compile and run main.cpp.

The team to pick will be output to stdout.

## team_data.txt

The parser converts this format:

    Name Stat1 Stat2
    #00 Joe Bloggs
    Metadata about him 111 222
    #05 Jane Doe
    Other data 333 444
    
and so on. The first line gives a list of stats to collect. Each player is then two lines: the first is a shirt number (ignored) and a name (assigned to `name`). The second line is metadata which is ignored - it will be parsed until it finds  a number - and then the values are assigned to the stats. So `Joe Bloggs` ends up with `Stat1=111` `Stat2=222` and `Jane Doe` ends up with `Stat1=333` `Stat2=444`. The name and number of stats can be set arbitrarily.

If you want a different format you'll have to edit/rewrite the `get_player()` function  and maybe the `read_header()` function too. 

## composition.txt

Any line beginning with `#` is a comment and will be ignored.

List offensive positions on a line starting with `Offence:` and defensive ones on a line starting `Defence:`. Both lines must contain an equal number of positions.

Positions can be repeated. An association football team would like like `GK LB CB CB RB LM CM CM RW ST ST` for example.

Set calculations based on stats.

If a goalkeeper has stats `Sav` and `Pos` you could set the goalkeeper's score with `GK = Sav + Pos` for example. The name of a stat can be used as a variable name and will be substituted in for the value of the stat associated with that player when evaluating that player in that position.

All names of positions, stats, and functions are NOT case sensitive. `sav`, `SAV`, `sAv`, etc... are all equivalent to each other.

The following mathematical operations are supported:

`+`, `-`, `*`, `/`, `(brackets)`, `^` (for exponents), `min` (with any number of arguments), `max` (with any number of arguments), `pow` (with two arguments): `pow(a,b)` is equivalent to `a^b`.

Values can be converted to `bool`s. Any value `abs(val)<0.5` is considered `false`, everything else is `true`. Conversely `false` converts to `0.0` and `true` converts to `1.0`.

The following boolean operations are supported: `&&` (and), `||` (or), `not`, `if(bool,true_expression,false_expression)`, `<`, `>`, `<=`, `>=`, `==` and `!=`.

## Method

First the `team_data.txt` file is converted to a vector of `Player` objects. Players are evaluated at each position, and their best offence and defence positions cached.

The team is sorted based on the total of best offence and best defence positions. The best players by this sort are set as starters and every permutation of offensive and defensive arrangements of those players are tried to get the offensive and defensive positions for that line up.

A team is scored by adding the scores of each player in their offensive and defensive positions.

Then the entire roster is iterated through looking for a player who is not in the line up. Then it will try swapping them in for each player, taking their offensive position. It will then find the best defensive line up again. The new line up is compared to the old one. If it scores better than the old one this setup replaces the old one and the process is restarted from the beginning of this paragraph. This repeats until no substitution improves the team.
