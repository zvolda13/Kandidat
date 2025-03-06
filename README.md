
hej hej

hej hej

/code
import bluetooth

def bluetooth_mirror():

    server_sock = bluetooth.BluetoothSocket(bluetooth.RFCOMM)
    
    port = bluetooth.PORT_ANY  # Use an available RFCOMM port
    
    server_sock.bind(("localhost", port))  # Bind to local Bluetooth interface
    
    server_sock.listen(1)  # Start listening for connections

    uuid = "00001101-0000-1000-8000-00805F9B34FB"  # UUID for Serial Port Profile (SPP)
    bluetooth.advertise_service(
        server_sock,
        "TNK132-spegel-Grupp_X",
        service_id=uuid,
        service_classes=[uuid, bluetooth.SERIAL_PORT_CLASS],
        profiles=[bluetooth.SERIAL_PORT_PROFILE]
    )

    print(f"Bluetooth server started on port {port}. Waiting for connections...")

    try:
        client_sock, client_info = server_sock.accept()  # Accept connection
        print(f"Connected to {client_info}")

        while True:
            print("Waiting for message...")
            data = client_sock.recv(1024)  # Receive up to 1024 bytes
            if not data:
                break

            message = data.decode("utf-8").strip()
            print(f"Received and echoing back: {message}")
            client_sock.sendall((message + "\n").encode("utf-8"))  # Send the same message back

        client_sock.close()
    except Exception as e:
        print(f"Error: {e}")
    finally:
        server_sock.close()
        print("Bluetooth server closed.")

if __name__ == "__main__":
    bluetooth_mirror()
