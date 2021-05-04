#a card game created

import random
SUITS = {
    "Diamonds": 1,
    "Hearts": 2,
    "Spades": 3,
    "Clubs": 4
}
RANKS = {
    "Two": 2,
    "Three": 3,
    "Four": 4,
    "Five": 5,
    "Six": 6,
    "Seven": 7,
    "Eight": 8,
    "Nine": 9,
    "Ten": 10,
    "Jack": 11,
    "Queen": 12,
    "King": 13,
    "Ace": 14
}

class PlayingCard:
    def __init__(self, rank, suit):
        self.suit = suit
        self.rank = rank
        self.face_up = False

    def name(self):
        return " ".join([self.rank, "of", self.suit])

class Deck:
    def __init__(self):
        self.cards = []
        for suit in SUITS:
            for rank in RANKS:
                self.cards.append(PlayingCard(rank, suit))
        self.shuffle()

    def add_card(self, card):
        self.cards.append(card)

    def shuffle(self):
        random.shuffle(self.cards)

    def remove_top_card(self):
        return self.cards.pop()

class Hand:
    def __init__(self):
        self.cards = []

    def add_card(self, card):
        self.cards.append(card)

    def card_by_index(self, index):
        try:
            return self.cards[index]
        except Exception:
            return None

    def remove_card(self):
        if not self.cards:
            return None
        return self.cards.pop()

class Player:
    def __init__(self, name):
        self.name = name
        self.hand = Hand()

class InternetStreamingView:
    def show_player_and_hand(self, player_name, hand):
        # Meaningful code goes here.
        pass

    def show_winner(self, winner_name):
        # Meaningful code goes here.
        pass


class BroadcastView:
    def show_player_and_hand(self, player_name, hand):
        # Meaningful code goes here.
        pass

    def show_winner(self, winner_name):
        # Meaningful code goes here.
        pass


class PlayerView:
    def prompt_for_new_player(self):
        new_player = input("Type name of player: ")
        if new_player == "":
            return None
        return new_player

    def show_player_and_hand(self, player_name, hand):
        print("[" + player_name + "]")
        for card in hand.cards:
            if card.face_up:
                print(card.name())
            else:
                print("(hidden card)")

    def prompt_for_flip_cards(self):
        print ("")
        prompt = input("Ready to see who won?")
        return True

    def show_winner(self, winner_name):
        print("")
        print("Congratulations", winner_name, "!")

    def prompt_for_new_game(self):
        print ("")
        while True:
            prompt = input("Play again? Y/N: ")
            if prompt == "Y":
                return True
            if prompt == "N":
                return False


class GameController:
    def __init__(self, deck,player_view, view_1, view_2, game_evaluator):
        # Model
        self.players = []
        self.deck = deck

        # View
        self.views = [player_view, view_1, view_2]
        self.player_dialogue = player_view

        #controller
        self.game_evaluator = game_evaluator


    def add_player(self, player_name):
        self.players.append(Player(player_name))

    def start_game(self):
        self.deck.shuffle()
        for player in self.players:
            next_card = self.deck.remove_top_card()
            if next_card is not None:
                player.hand.add_card(next_card)


    def rebuild_deck(self):
        for player in self.players:
            while player.hand.cards:
                this_card = player.hand.remove_card()
                this_card.face_up = False
                self.deck.add_card(this_card)
        self.deck.shuffle()

    def run(self):
        while len(self.players) < 5:
            for view in self.views:
                new_player = view.prompt_for_new_player()
                if new_player is None:
                    break
                self.add_player(new_player)

        while True:
            self.start_game()
            for view in self.views:
                for player in self.players:
                    view.show_player_and_hand(player.name, player.hand)

            self.view.prompt_for_flip_cards()
            for player in self.players:
                for card in player.hand.cards:
                    card.face_up = True
                for view in self.views:
                    view.show_player_and_hand(player.name, player.hand)

            for view in self.views:
                view.show_winner(self.game_evaluator.find_winner(self.players))
            for view in self.views:
                if not self.view.prompt_for_new_game():
                    break
            else:
                self.rebuild_deck()
                continue

            break

class HighCardGameEvaluator:
    def find_winner(self, players):
        best_rank = None
        best_rank_suit = None
        best_candidate = None

        for player in self.players:
            this_rank = RANKS[player.hand.card_by_index(0).rank]
            this_suit = SUITS[player.hand.card_by_index(0).suit]
            if (best_rank is None
                    or (this_rank > best_rank)
                    or (this_rank == best_rank and this_suit > best_rank_suit)
                ):
                best_candidate = player.name
                best_rank = this_rank
                best_rank_suit = this_suit

            return best_candidate


# Calling code to build MVC and start the Controller.
deck = Deck()

player_view = PlayerView()
view_1 = BroadcastView()
view_2 = InternetStreamingView()
game_evaluator =  HighCardGameEvaluator()

controller = GameController(deck, player_view, views_1, view_2, game_evaluator)
controller.run()






