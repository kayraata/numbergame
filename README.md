import Debug "mo:base/Debug";
import Int "mo:base/Int";
import Nat "mo:base/Nat";
import Text "mo:base/Text";
import Array "mo:base/Array";

actor NumberGame {
    // State variables
    private stable var targetNumber : Nat = 42; // Default target
    private stable var lastGuess : ?Nat = null;
    private stable var gameActive : Bool = false;

    // Helper function to calculate the absolute difference between numbers
    private func absDiff(a : Nat, b : Nat) : Nat {
        if (a > b) {
            return a - b;
        };
        return b - a;
    };

    // Start a new game with a random number between 1 and maxRange
    public func startNewGame(maxRange : Nat) : async Text {
        // Simple pseudo-random number generation
        // In a real implementation, you'd want a better random number generator
        targetNumber := (targetNumber * 7 + 13) % maxRange + 1;
        gameActive := true;
        lastGuess := null;
        return "New game started! Guess a number between 1 and " # Nat.toText(maxRange);
    };

    // Make a guess and get feedback
    public func makeGuess(guess : Nat) : async Text {
        if (not gameActive) {
            return "No active game. Please start a new game first.";
        };

        if (guess == targetNumber) {
            gameActive := false;
            return "Congratulations! You found the number: " # Nat.toText(targetNumber);
        };

        let currentDiff = absDiff(guess, targetNumber);
        
        // Compare with last guess if it exists
        switch (lastGuess) {
            case (null) {
                lastGuess := ?guess;
                if (currentDiff <= 5) {
                    return "Very warm!";
                } else if (currentDiff <= 10) {
                    return "Warm";
                } else {
                    return "Cold";
                };
            };
            case (?prevGuess) {
                let prevDiff = absDiff(prevGuess, targetNumber);
                lastGuess := ?guess;
                
                if (currentDiff < prevDiff) {
                    if (currentDiff <= 5) {
                        return "Getting very warm! You're closer than before!";
                    } else if (currentDiff <= 10) {
                        return "Getting warmer! You're closer than before!";
                    } else {
                        return "Getting warmer, but still cold!";
                    };
                } else {
                    if (currentDiff <= 5) {
                        return "Still very warm, but getting colder!";
                    } else if (currentDiff <= 10) {
                        return "Getting colder, but still warm!";
                    } else {
                        return "Getting colder!";
                    };
                };
            };
        };
    };

    // Get current game status
    public query func getGameStatus() : async Text {
        if (gameActive) {
            return "Game is active. Keep guessing!";
        } else {
            return "No active game. Start a new game to play!";
        };
    };
};
