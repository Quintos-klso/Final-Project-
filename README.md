# Text Adventure Game: The Quest for the Crystal Key within a tower.

GAME_WORLD = {
    "entrance": {
        "description": "You stand at the entrance of an ancient castle. Stone walls loom above you.",
        "exits": {"east": "great_hall", "north": "tower"},
        "items": ["rusty_key"],
    },
    "great_hall": {
        "description": "A vast hall with high ceilings and tapestries adorning the walls.",
        "exits": {"west": "entrance", "east": "library", "north": "throne_room", "south": "crypt"},
        "items": ["torch"],
    },
    "tower": {
        "description": "You climb a spiral staircase in a tall tower. The air is cold and damp.",
        "exits": {"south": "entrance", "up": "tower_top"},
        "items": ["scroll"],
    },
    "tower_top": {
        "description": "At the top of the tower, you find a small chamber. A crystal box sits on a pedestal.",
        "exits": {"down": "tower"},
        "items": ["crystal_key"],
    },
    "library": {
        "description": "Shelves of ancient books line the walls. The smell of old parchment fills the air.",
        "exits": {"west": "great_hall", "south": "garden"},
        "items": ["ancient_book"],
    },
    "throne_room": {
        "description": "You stand in the grandest room of the castle. A massive throne sits at the far end.",
        "exits": {"south": "great_hall"},
        "items": [],
    },
    "crypt": {
        "description": "Dark stone tombs line the walls of this underground chamber. Torches flicker on the walls.",
        "exits": {"north": "great_hall", "east": "secret_chamber"},
        "items": ["golden_coin"],
    },
    "secret_chamber": {
        "description": "A hidden chamber reveals ancient treasures and magical artifacts.",
        "exits": {"west": "crypt"},
        "items": ["mystical_gem"],
    },
    "garden": {
        "description": "An overgrown garden with exotic plants and flowers. A fountain sits in the center.",
        "exits": {"north": "library"},
        "items": ["silver_seed"],
    },
}


class Game:
    def __init__(self):
        self.current_room = "entrance"
        self.inventory = []
        self.game_over = False
        self.won = False

    def display_room(self):
        room = GAME_WORLD[self.current_room]
        print("\n" + "=" * 60)
        print(f"[{self.current_room.upper().replace('_', ' ')}]")
        print("=" * 60)
        print(room["description"])
        if room["items"]:
            print(f"\nItems here: {', '.join(room['items'])}")
        print(f"\nExits: {', '.join(room['exits'].keys())}")

    def show_inventory(self):
        print("\n--- INVENTORY ---")
        if self.inventory:
            for item in self.inventory:
                print(f"  - {item.replace('_', ' ').title()}")
        else:
            print("  (empty)")

    def move(self, direction):
        room = GAME_WORLD[self.current_room]
        if direction in room["exits"]:
            self.current_room = room["exits"][direction]
            print(f"\nYou move {direction}...")
            self.display_room()
        else:
            print(f"\nYou can't go {direction} from here.")

    def get_item(self, item_name):
        room = GAME_WORLD[self.current_room]
        item_name_lower = item_name.lower().replace(" ", "_")
        if item_name_lower in room["items"]:
            room["items"].remove(item_name_lower)
            self.inventory.append(item_name_lower)
            print(f"\nYou picked up: {item_name_lower.replace('_', ' ').title()}")
        else:
            print(f"\nThere is no '{item_name}' here.")

    def check_win_condition(self):
        if self.current_room == "throne_room" and "crystal_key" in self.inventory:
            self.won = True
            return True
        return False

    def display_help(self):
        print("\n--- AVAILABLE COMMANDS ---")
        print("  go [direction]     - Move in a direction (north, south, east, west, up, down)")
        print("  get [item]         - Pick up an item in the current room")
        print("  inventory          - Display your inventory")
        print("  look               - Look around the current room again")
        print("  help               - Show this help message")
        print("  quit               - Exit the game")

    def process_command(self, user_input):
        command_parts = user_input.strip().lower().split(maxsplit=1)
        if not command_parts:
            return
        command = command_parts[0]
        argument = command_parts[1] if len(command_parts) > 1 else None

        if command == "go":
            if argument:
                self.move(argument)
            else:
                print("Go where? Specify a direction (north, south, east, west, up, down)")
        elif command == "get":
            if argument:
                self.get_item(argument)
            else:
                print("Get what? Specify an item name.")
        elif command == "inventory":
            self.show_inventory()
        elif command == "look":
            self.display_room()
        elif command == "help":
            self.display_help()
        elif command == "quit":
            print("\nThanks for playing! Goodbye!")
            self.game_over = True
        else:
            print(f"Unknown command: '{command}'. Type 'help' for available commands.")

    def run(self):
        print("\n" + "=" * 60)
        print("WELCOME TO THE CRYSTAL KEY QUEST")
        print("=" * 60)
        print("A text adventure game where you must find the crystal key")
        print("and bring it to the throne room to win!")
        print("\nType 'help' to see available commands.")
        self.display_room()

        while not self.game_over:
            if self.check_win_condition():
                print("\n" + "!" * 60)
                print("VICTORY! You have brought the crystal key to the throne room!")
                print("The castle is restored to its former glory!")
                print("!" * 60)
                self.game_over = True
                break

            try:
                user_input = input("\n> ").strip()
                if user_input:
                    self.process_command(user_input)
            except KeyboardInterrupt:
                print("\n\nGame interrupted. Goodbye!")
                self.game_over = True
            except EOFError:
                print("\n\nEnd of input. Goodbye!")
                self.game_over = True


def main():
    game = Game()
    game.run()


if __name__ == "__main__":
    main()
