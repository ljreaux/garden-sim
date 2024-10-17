# Garden Simulator

This is a beginner python project from Dataquest. The project can be found [here](https://www.dataquest.io/projects/guided-project-a-garden-simulator-text-based-game/)

## Set Up Plant Class


```python
title = "Garden Simulator"
import random
class Plant:
  def __init__(self, name, harvest_yield):
    self.name = name
    self.harvest_yield = harvest_yield
    self.growth_stages = ['seed', 'sprout', 'plant', 'flower', 'harvest-ready']
    self.current_growth_stage = self.growth_stages[0]
    self.harvestable = False
    
# Step 3
  
  def grow(self): 
    
    def check_harvestable():
      if self.current_growth_stage == self.growth_stages[-1]:
        self.harvestable = True
        print(f"{self.name} is now harvestable.")
      return self.harvestable
    
    if check_harvestable():
      print(f"{self.name} is fully grown.")
    else:
      index = self.growth_stages.index(self.current_growth_stage)
      self.current_growth_stage = self.growth_stages[index + 1]
      check_harvestable()
  
  def harvest(self):
    if self.harvestable:
      self.harvestable = False
      return self.harvest_yield
    else:
      print(f"{self.name} is not harvestable yet.")
      return None
    
```

## Define Plant Types


```python
class Tomato(Plant):
  def __init__(self):
    super().__init__("Tomato", 5)
    self.growth_stages[3] = 'fruiting'
    
class Lettuce(Plant):
  def __init__(self):
    super().__init__("Lettuce", 1)

class Carrot(Plant):
  def __init__(self):
    super().__init__("Carrot", 3)
    self.growth_stages[3] = 'root-developing'

class Strawberry(Plant):
  def __init__(self):
    super().__init__("Strawberry", 4)
    self.growth_stages[3] = 'berry-developing'

class Potato(Plant):
  def __init__(self):
    super().__init__("Potato", 6)
    self.growth_stages[3] = 'tuber-developing'
```

## Player Input


```python
def select_item(items) -> Plant | str | None:
  items_list = []
  if type(items) is dict:
    items_list = list(items.keys())
  elif type(items) is list:
    items_list = items
  else:
    print("Invalid input. Please select from the provided options.")
    return None
  
  for i in range(len(items_list)):
        try:
            item_name = items_list[i].name
        except:
            item_name = items_list[i]
        print(f"{i + 1}. {item_name}")
  
  while True:
    try:
      choice = int(input('Enter the number of your choice. '))
      
      if  0 < choice <= len(items_list):
        return items_list[choice - 1]
      else:
        print("Invalid choice. Please try again.")
    except ValueError:
      print("Invalid input. Please enter a number.")
      continue

```

## Define Gardener Class


```python
types_of_plants =["tomato", "lettuce", "carrot", "strawberry", "potato"]

class Gardener():
  plant_dict = {
    "tomato": Tomato, 
    "lettuce": Lettuce, 
    "carrot": Carrot, 
    "strawberry": Strawberry, 
    "potato": Potato
}
  def __init__(self, name):
    self.name = name
    self.planted_plants: list[Plant] = []
    self.inventory = {
      "tomato": 1, 
      "lettuce": 5, 
      "carrot": 8, 
      "strawberry": 12, 
      "potato": 6
    }
  
  def plant(self):
    player_input = select_item(self.inventory)
    amt_in_inventory = self.inventory[player_input]
    if amt_in_inventory > 0:
      self.inventory[player_input] -= 1
      new_plant = self.plant_dict[player_input]()
      self.planted_plants.append(new_plant)
      print(f"{self.name} planted a {player_input} plant.")
      
      if self.inventory[player_input] == 0:
        del self.inventory[player_input]
    else:
      print(f"{self.name} has no {player_input} in stock.")
  
  def tend(self):
    for plant in self.planted_plants:
      if plant.harvestable:
        print(f'{plant.name} is ready to harvest!')
      else:
        plant.grow()
      print(f'{plant.name} is at the {plant.current_growth_stage} stage.')
  
  def harvest(self):
    selected_plant = select_item(self.planted_plants)
    
    if selected_plant.harvestable:
      if selected_plant.name in self.inventory:
        self.inventory[selected_plant.name] += selected_plant.harvest()
      else:
        self.inventory[selected_plant.name] = selected_plant.harvest()
      print(f"You harvested a {selected_plant.name}!")
      self.planted_plants.remove(selected_plant)
    else:
      print(f"You can't harvest a {selected_plant.name}!")
        
  def forage_for_seeds(self):
    seed = random.choice(types_of_plants)
    if seed not in self.inventory:
      self.inventory[seed] = 1
    else:
      self.inventory[seed] += 1
    print(f"{self.name} found a {seed} seed!")
```

## Game Loop


```python
# Get the Player's name
player_name = input(f"Welcome to {title}. Please enter your name to begin the game. ")

print(f"Welcome {player_name}! Enter 'help' at any time to see a list of commands. Enter 'exit' to exit the game.")

# Create a new Gardener object
gardener = Gardener(player_name)

while True:
  commands = ['forage', 'plant', 'tend', 'harvest', 'quit', 'help']
  print('\nWhat would you like to do?\n')
  for i, command in enumerate(commands, start=1):
    print(command)
  command = input().lower()
  if command not in commands:
    print('Invalid command. Please try again.')
    continue
  match command:
    case 'forage':
      gardener.forage_for_seeds()
    case 'plant':
      gardener.plant()
    case 'tend':
      gardener.tend()
    case 'harvest':
      gardener.harvest()
    case 'quit':
      print(f'Goodbye {gardener.name}! Thanks for playing {title}.')
      break
    case 'help':
      print('\nHere are the available commands:')
      print('forage: Find a seed for a new plant.')
      print('plant: Plant a seed.')
      print('tend: Gently care for your plants.')
      print('harvest: Harvest a ready plant.')
      print('quit: Exit the game.')
```

    Welcome Larry! Enter 'help' at any time to see a list of commands. Enter 'exit' to exit the game.
    
    What would you like to do?
    
    forage
    plant
    tend
    harvest
    quit
    help
    1. tomato
    2. lettuce
    3. carrot
    4. strawberry
    5. potato
    Larry planted a potato plant.
    
    What would you like to do?
    
    forage
    plant
    tend
    harvest
    quit
    help
    Invalid command. Please try again.
    
    What would you like to do?
    
    forage
    plant
    tend
    harvest
    quit
    help
    Larry found a lettuce seed!
    
    What would you like to do?
    
    forage
    plant
    tend
    harvest
    quit
    help
    1. tomato
    2. lettuce
    3. carrot
    4. strawberry
    5. potato
    Larry planted a lettuce plant.
    
    What would you like to do?
    
    forage
    plant
    tend
    harvest
    quit
    help
    Potato is at the sprout stage.
    Lettuce is at the sprout stage.
    
    What would you like to do?
    
    forage
    plant
    tend
    harvest
    quit
    help
    Potato is at the plant stage.
    Lettuce is at the plant stage.
    
    What would you like to do?
    
    forage
    plant
    tend
    harvest
    quit
    help
    Potato is at the tuber-developing stage.
    Lettuce is at the flower stage.
    
    What would you like to do?
    
    forage
    plant
    tend
    harvest
    quit
    help
    Potato is now harvestable.
    Potato is at the harvest-ready stage.
    Lettuce is now harvestable.
    Lettuce is at the harvest-ready stage.
    
    What would you like to do?
    
    forage
    plant
    tend
    harvest
    quit
    help
    1. Potato
    2. Lettuce
    You harvested a Potato!
    
    What would you like to do?
    
    forage
    plant
    tend
    harvest
    quit
    help
    1. Lettuce
    You harvested a Lettuce!
    
    What would you like to do?
    
    forage
    plant
    tend
    harvest
    quit
    help
    Invalid command. Please try again.
    
    What would you like to do?
    
    forage
    plant
    tend
    harvest
    quit
    help
    Here are the available commands:
    forage: Find a seed for a new plant.
    plant: Plant a seed.
    tend: Gently care for your plants.
    harvest: Harvest a ready plant.
    quit: Exit the game.
    
    What would you like to do?
    
    forage
    plant
    tend
    harvest
    quit
    help
    Goodbye Larry! Thanks for playing Garden Simulator.

