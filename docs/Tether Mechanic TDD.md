Tether Mechanic TDD
# Requirements/Features
* Attach any item to a tool or armor which support the following:
  * A change in tool stats (more range, more damage, different durability)
  * Additional behavior on left click use
    * Merge with piston will knock back
    * Merge with flint steel will put on fire
    * Merge with lava bucket will put on fire
  * Additional behavior on right click
    * Merge with piston will knock back (same behavior)
    * Merge with flint steel will spawn fire
    * Merge with lava bucket will place lava
  * An unlimited amount of items need to be able to be attached
* You can discard all attached items

# Implementation
```rust // just cause, it gives nice highlighting
// A struct with all stat adjusters, they will simply store the deltas so we can 
// apply and undo
struct StatAdjusters {    
    float durability
    float range
    float health
    etc...
}

// Tools and armor will inherit from this. 
class IAttacher{    
    attachables: array<IAttachable>
    statAdjusters: OriginalStats
    fn onLeftUse(damage) {       // Allows us to impl any behavior when we left click the item
        statAdjusters = getStatAdjusters;
        damage += statAdjusters.damage;
    }
    fn onRightUse       // Same for right click
    fn onReceiveDamage  // For custom behavior with armor
    // Create a stat adjuster from all attachables. We will call and apply this every time
    // the info is required. I'd rather avoid patching the tool stats so that we can easily 
    // roll back the original stats (simply by not applying the stat adjusters), but perhaps we need it 
    fn getStatAdjusters {
        statAdjuster = new StatAdjuster
        foreach attachables
            statAdjuster += attachble.getStatAdjusters();
            
        return statAdjuster;
    } 
    // So just to be sure, we have add and remove all. So we can control applying updated tool stats
    fn addAttachable
    fn removeAttachables
}

Tool: IAttacher {
    fn onLeftUse {
        
    }
}

// Inherited by everything that can be attached
// IAttacher will call the methods for all IAttachables that are on it, we will add any data we need to this
class IAttachable{    
    fn onLeftUse
    fn onRightUse
    fn onReceiveDamage
    fn getStatAdjuster
}

DirtBlock implements IAttachable {
    fn getStatAdjuster {
        return new StatAdjuster{
            range = 1,
            damage = 1
        }
    }
}

DiamondBlock implements IAttachable {
    fn getStatAdjuster {
        return new StatAdjuster{
            range = 5,
            damage = 2,
            durability = -100
        }
    }    
}

// Protype.java
{
    Ore implements IAttachable {
        fn getStatAdjuster {
            return new StatAdjuster{
                range = 5,
                damage = 2,
            }
        }    
    }

    IronOre implement Ore;
    StoneOre implement Ore;
    DiamondOre implement Ore;
    RedstoneOre implement Ore;
    IronOre implement Ore;

    DiamondBlock implements IAttachable {
        fn getStatAdjuster {
            return new StatAdjuster{
                range = 5,
                damage = 2,
            }
        }    
    }

    DirtBlock implements IAttachable {
        fn getStatAdjuster {
            return new StatAdjuster{
                range = 1,
                damage = 2
            }
        }
    }
}

// Protype.json 
{
    "DiamondBlock": {
        "range": 1,
        "damage": 2
    },
    "DirtBlock": {
        "range": 1,
        "damage": 2 
    },
    "Ore": {
        "damage": 3  
        "range": 2
    },
    "StoneOre": "Ore",
    "IronOre": "Ore",
    "DiamondOre": "Ore",
}
```

MoneyOres.java...

interface IOre {
    fn GetMoneyValue
}

DiamonOre extends IOre {
    fn GetMoneyValue {return 100;}
}

IronOre extends IOre {
    fn GetMoneyValue {return 10;}
}

fn PlayerPickedUpItem {
    ore = IOre(pickedUpItem);
    if(ore) {
        OurMoney += ore.GetMoneyValue();
    }     
}
