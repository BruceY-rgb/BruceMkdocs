![alt text](image-31.png)
![alt text](31a65f7199d1c0d8b28bff12e320812.jpg)
![alt text](image-32.png)
![alt text](8dc9c0bd2fd9ff2c0a9def8d20e30ed.jpg)
![alt text](image-29.png)
![alt text](image-30.png)
a.![alt text](9d8766282b67c78c3ab078b6d8e124f.jpg)
b.![alt text](11142f8618a27ed7cf4b6a0e80f4d67.jpg)
![alt text](image-28.png)
![alt text](265ecd5c56bfc404390a363366ef75f.jpg)
customer(
    <u>customer_id</u>,
    name,
    email,
    phone,
    passport
)

reservation(
    <u>customer_id,res_date,res_flight</u>,seat_no
)
- res_flight is the foreign key references flight


flight(
    <u>flight_id</u>,
    airline,
    departure
    arrival
    status
    capacity,
    start_date,
    end_date,
    frequency,
    route_id
)

- route_id is the foreign key references route

route(  <u>route_id</u>,
    origin,
    destination,
    stops
)