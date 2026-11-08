import json
from pathlib import Path

FILE = Path("movies.json")

def load_movies():
    if FILE.exists():
        return json.loads(FILE.read_text(encoding="utf-8"))
    return []

def save_movies(movies):
    FILE.write_text(
        json.dumps(movies, indent=4, ensure_ascii=False),
        encoding="utf-8"
    )

def add_movie(movies):
    title = input("Movie title: ").strip()
    genre = input("Genre: ").strip()

    if not title:
        print("Title is required.")
        return

    movies.append({
        "title": title,
        "genre": genre,
        "watched": False,
        "rating": None
    })

    save_movies(movies)
    print("Movie added!")

def show_movies(movies):
    if not movies:
        print("Watchlist is empty.")
        return

    for i, movie in enumerate(movies, 1):
        status = "Watched" if movie["watched"] else "Not watched"
        rating = movie["rating"] or "Not rated"

        print(f"{i}. {movie['title']}")
        print(f"   Genre: {movie['genre']}")
        print(f"   Status: {status} | Rating: {rating}/10")

def rate_movie(movies):
    show_movies(movies)

    try:
        index = int(input("Movie number: ")) - 1
        movie = movies[index]

        rating = int(input("Rating (1-10): "))

        if not 1 <= rating <= 10:
            print("Rating must be between 1 and 10.")
            return

        movie["rating"] = rating
        movie["watched"] = True

        save_movies(movies)
        print("Rating saved!")

    except (ValueError, IndexError):
        print("Invalid input.")

def main():
    movies = load_movies()

    while True:
        print("\n1. Add movie")
        print("2. Show watchlist")
        print("3. Rate movie")
        print("4. Show average rating")
        print("5. Exit")

        choice = input("Choose: ")

        if choice == "1":
            add_movie(movies)

        elif choice == "2":
            show_movies(movies)

        elif choice == "3":
            rate_movie(movies)

        elif choice == "4":
            ratings = [
                m["rating"] for m in movies
                if m["rating"] is not None
            ]

            if ratings:
                print(f"Average rating: {sum(ratings) / len(ratings):.2f}/10")
            else:
                print("No ratings available.")

        elif choice == "5":
            break

        else:
            print("Invalid choice.")

if __name__ == "__main__":
    main()
