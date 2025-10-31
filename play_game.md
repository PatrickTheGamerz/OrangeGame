# play_game.py
from tic_tac_toe import TicTacToe

def main():
    game = TicTacToe()
    winner = None

    while not game.is_full() and winner is None:
        game.print_board()
        try:
            move = int(input(f"Player {game.current_player}, choose (0-8): "))
        except ValueError:
            print("Invalid input. Enter a number 0-8.")
            continue

        if move < 0 or move > 8:
            print("Out of range. Try again.")
            continue

        if not game.make_move(move):
            print("Spot taken. Try again.")
            continue

        winner = game.check_winner()
        if winner is None:
            game.switch_player()

    game.print_board()
    if winner:
        print(f"Player {winner} wins!")
    else:
        print("It's a tie!")

if __name__ == "__main__":
    main()
