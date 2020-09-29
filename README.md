# wiki-enumerators

[Enumerated types (enum)](https://en.wikipedia.org/wiki/Enumerated_type) are data types that allow us to represent categorical variables consisting of a set (sometimes list) of named values. 

### Examples of enum in javascript

```
export enum FunctionType {
  RESIDENTIAL = "living the life",
  COMMERCIAL = "money, money, money",
}

.
.
.

console.log(FunctionType.RESIDENTIAL)
>> "living the life"

console.log(FunctionType["COMMERCIAL"]
>> "money, money, money"]

console.log(FunctionType[0])
>> syntax error! javascript doesn't let you do this.
```


#### Enums are usually injective, meaning the names (keys) must be unique, but the values can repeat.

example of an incorrect, and non-injective enumerator:
```
export enum FunctionType {
  RESIDENTIAL = "living the life",
  COMMERCIAL = "money, money, money",
  RESIDENTAL = "welcome to my crib", // <-- bruh what are you thinking?
}
```

#### You are a coding pro and don't use magic numbers and strings? Then you probably already (want to) use enumerators.

replace this
```
export type NoiseStats = {
  all_units: {
    analysis: NoiseAnalysisUnitStats[];
  };
  ["apartment"]: {
    analysis: NoiseAnalysisUnitStats[];
    key_figures: {
      fraction_of_valid_apartments: number;
    };
  };
  ["facade"]: NoiseStat;
  ["ground"]: NoiseStat;
  ["surrounding"]: NoiseStat;
  units: {
    analysis: NoiseAnalysisUnitStats[];
  };
};

.
.
.

const facadeStats = getStats(roadLimits, railLimits, noiseStats["ficad"], analysis); // TODO: duble czech spehling 
const groundStats = getStats(roadLimits, railLimits, noiseStats["gwownd"], analysis);
const surroundingStats = getStats(roadLimits, railLimits, noiseStats["sirowndin"], analysis);
```

with this
```
export enum NoiseStatKey {
  APARTMENT = "apartment",
  FACADE = "facade",
  GROUND = "ground",
  SURROUNDING = "surrounding",
}

export type NoiseStats = {
  all_units: {
    analysis: NoiseAnalysisUnitStats[];
  };
  [NoiseStatKey.APARTMENT]: {
    analysis: NoiseAnalysisUnitStats[];
    key_figures: {
      fraction_of_valid_apartments: number;
    };
  };
  [NoiseStatKey.FACADE]: NoiseStat;
  [NoiseStatKey.GROUND]: NoiseStat;
  [NoiseStatKey.SURROUNDING]: NoiseStat;
  units: {
    analysis: NoiseAnalysisUnitStats[];
  };
};

.
.
.

const facadeStats = getStats(roadLimits, railLimits, noiseStats[NoiseStatKey.FACADE], analysis); // syntax checking 
const groundStats = getStats(roadLimits, railLimits, noiseStats[NoiseStatKey.GROUND], analysis); // automatically 
const surroundingStats = getStats(roadLimits, railLimits, noiseStats[NoiseStatKey.SURROUNDING], analysis); // checks this
```

As you can see, we can centralize our source of truth on names of strings and use syntax checking (and auto-complete) to write this code. Then when you change your mind (there are only two hard things in computer science, cache invalidation and naming things) you only need to change the value in the enumerator without any `Ctrl + f`!

If you didn't know about enumerators, you probably knew that fancy languages like javascript had, well, some fancy stuff. But Python is not fancy, so it can't have enums: [WRONG](https://docs.python.org/3/library/enum.html)!

```
import typing
from enum import Enum

class Color(Enum):
  RED = "rojo"
  GREEN = "verde"
  BLUE = "azul"
  
class Pirate(Enum):
  JACK = "Jack Sparrow"
  DAVY = "Davy Jones"


class SailBoat:
  def __init__(self, color: Color, passengers: List[str] = None):
    self.color = color.value
    self.passengers = passengers if passengers is not None else [Pirate.JACK.value, Pirate.DAVY.value]
  
  def __str__(self):
    if Pirate.JACK.value in self.passengers or Pirate.DAVY.value in self.passengers:
      return "aaaarg!"
    return "Hei, Hei!"
    
hello_world = SailBoat(Color.RED)

print(hello_world)

>> "aaaarg!"
```

now we can fix this up a bit and remove all the `.value`s, which can cause errors later on.


```
import typing
from enum import Enum

class Color(str, Enum):
  RED = "rojo"
  GREEN = "verde"
  BLUE = "azul"
  
class Pirate(str, Enum):
  JACK = "Jack Sparrow"
  DAVY = "Davy Jones"


class SailBoat:
  def __init__(self, color: Color, passengers: List[str] = None):
    self.color = color
    self.passengers = passengers if passengers is not None else [Pirate.JACK, Pirate.DAVY]
  
  def __str__(self):
    if Pirate.JACK in self.passengers or Pirate.DAVY in self.passengers:
      return "aaaarg!"
    return "Hei, Hei!"
    
hello_world = SailBoat(Color.RED)

print(hello_world)

>> "aaaarg!"
```

now things like `Pirate.JACK` returns the string `"Jack Sparrow"` instead of `<Pirate.JACK: "Jack Sparrow">`.

I prefer to specify a type (like `str` or `int`) so that we can avoid all these `.value` calls. 

Another thing you can do is treat your enum datatype like an actual enumerator (iterator) by simply listing it out:

```
print(list(Pirate))

>> ["Jack Sparrow", "Davy Jones"]
```

and if you want to type check a list of enums then you can do this:

```
def walk_the_plank(pirates: List[Pirate]):
  for p in pirates:
    if p == p.JACK:
      print(“No survivors? Then where do the stories come from, I wonder. \n”)
      return
  print("you'll never get away with this \n")
  
walk_the_plank(list(Pirate))
>> “No survivors? Then where do the stories come from, I wonder."
>> "you'll never get away with this"

walk_the_plank([Pirate.JACK])
>> “No survivors? Then where do the stories come from, I wonder."
```





