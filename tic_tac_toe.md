# tic_tac_toe.py

class TicTacToe:
    def __init__(self):
        self.board = [" " for _ in range(9)]
        self.current_player = "X"

    def print_board(self):
        for row in [self.board[i:i+3] for i in range(0, 9, 3)]:
            print("|".join(row))
            print("-"*5)

    def make_move(self, position):
        if self.board[position] == " ":
            self.board[position] = self.current_player
            return True
        return False
        
    def switch_player(self):
        self.current_player = "O" if self.current_player == "X" else "X"

    def check_winner(self):
        win_conditions = [
            [0,1,2],[3,4,5],[6,7,8], # rows
            [0,3,6],[1,4,7],[2,5,8], # cols
            [0,4,8],[2,4,6]          # diagonals
        ]
        for cond in win_conditions:
            if self.board[cond[0]] == self.board[cond[1]] == self.board[cond[2]] != " ":
                return self.board[cond[0]]
        return None

    def is_full(self):
        return " " not in self.board
