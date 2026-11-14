import csv
import os

class Flight:
    def __init__(self, flight_no, origin, destination, seats, fare):
        self.flight_no = flight_no
        self.origin = origin
        self.destination = destination
        self.total_seats = int(seats)
        self.seats_left = int(seats)
        self.fare = float(fare)

    def book_seats(self, count):
        if count < 1:
            raise ValueError("Number of seats must be at least 1.")
        if count > self.seats_left:
            raise ValueError(f"Only {self.seats_left} seat(s) available on {self.flight_no}.")
        self.seats_left -= count

    def cancel_seats(self, count):
        self.seats_left = min(self.total_seats, self.seats_left + count)


class FlightBookingSystem:
    def __init__(self, csv_file="flights.csv"):
        self.flights = []
        self.bookings = {}
        self.next_booking_id = 1
        self.load_flights(csv_file)

    def load_flights(self, csv_file):
        if not os.path.exists(csv_file):
            raise FileNotFoundError(f"Flight data file not found: {csv_file}")
        with open(csv_file, newline="", encoding="utf-8") as file:
            for row in csv.DictReader(file):
                self.flights.append(Flight(
                    row["FlightNo"], row["Origin"], row["Destination"],
                    row["Seats"], row["Fare"]
                ))

    def search_flights(self, origin, destination):
        return [
            flight for flight in self.flights
            if flight.origin.casefold() == origin.strip().casefold()
            and flight.destination.casefold() == destination.strip().casefold()
        ]

    def book_flight(self, flight_no, passenger, seats):
        flight = next(
            (f for f in self.flights if f.flight_no.casefold() == flight_no.strip().casefold()),
            None
        )
        if flight is None:
            raise ValueError(f"Flight {flight_no} not found.")
        seats = int(seats)
        if not passenger.strip():
            raise ValueError("Passenger name cannot be empty.")
        flight.book_seats(seats)

        booking_id = f"BK{self.next_booking_id}"
        self.next_booking_id += 1
        total = seats * flight.fare
        self.bookings[booking_id] = {
            "flight": flight, "passenger": passenger.strip(),
            "seats": seats, "total": total
        }
        return booking_id, total, flight.seats_left

    def cancel_booking(self, booking_id):
        booking = self.bookings.pop(booking_id.strip().upper(), None)
        if booking is None:
            raise ValueError(f"Booking ID {booking_id} not found.")
        booking["flight"].cancel_seats(booking["seats"])
        return booking

    def display_flights(self, flights=None):
        for f in flights if flights is not None else self.flights:
            print(f"{f.flight_no} | {f.origin} -> {f.destination} | "
                  f"Seats: {f.seats_left} | Fare: Rs. {f.fare:.0f} per seat")


def main():
    try:
        system = FlightBookingSystem()
    except (FileNotFoundError, KeyError) as error:
        print("Unable to load flight data:", error)
        return

    while True:
        print("\\n--- FLIGHT BOOKING MANAGEMENT SYSTEM ---")
        print("1. Search flights\\n2. Book flight\\n3. Cancel booking\\n4. View flights\\n5. Exit")
        choice = input("Enter choice: ").strip()

        try:
            if choice == "1":
                origin = input("Origin: ")
                destination = input("Destination: ")
                results = system.search_flights(origin, destination)
                if results:
                    system.display_flights(results)
                else:
                    print("No flights found for this route.")

            elif choice == "2":
                flight_no = input("Flight number: ")
                passenger = input("Passenger name: ")
                seats = int(input("Number of seats: "))
                booking_id, total, left = system.book_flight(flight_no, passenger, seats)
                print(f"Booking confirmed. ID: {booking_id} | {passenger} | "
                      f"{flight_no.upper()} | {seats} seat(s) | Total: Rs. {total:.0f} "
                      f"| Seats left: {left}")

            elif choice == "3":
                booking_id = input("Booking ID: ")
                booking = system.cancel_booking(booking_id)
                print(f"Booking {booking_id.upper()} cancelled successfully.")

            elif choice == "4":
                system.display_flights()

            elif choice == "5":
                print("Thank you for using the system.")
                break
            else:
                print("Invalid menu choice. Please select 1-5.")

        except ValueError as error:
            print("Error:", error)


if __name__ == "__main__":
    main()
