
### Steps to run `C-SFRAT` 1.0 linux version on macOS 26

1. Install Docker Desktop and XQuartz
2. Setup XQuartz

   - Run in Mac terminal
   ```bash
   defaults write org.xquartz.X11 enable_iglx -bool true
   ```
   - Open XQuartz.
   - Go to the menu XQuartz > Settings (or Preferences).
   - Click the Security tab.
   - Check the box "Allow connections from network clients".
   - Restart XQuartz (Quit it completely and reopen it).
   - Run in Mac terminal
   ```bash
   xhost +localhost
   ```

3. Create the `Dockerfile`

    In the same folder as your `C-SFRAT` executable, create a new file named `Dockerfile` with the following content

    ```dockerfile
    # Use the x86_64 version of Ubuntu
    FROM --platform=linux/amd64 ubuntu:latest

    # Prevent interactive prompts during installation
    ENV DEBIAN_FRONTEND=noninteractive

    # Install all the libraries we identified
    RUN apt-get update && apt-get install -y \
        libgl1 \
        libglib2.0-0 \
        libsm6 \
        libxext6 \
        libxrender1 \
        libgl1-mesa-dri \
        fontconfig \
        fonts-dejavu \
        && rm -rf /var/lib/apt/lists/*

    # Set the working directory
    WORKDIR /app

    # The command to run when the container starts
    CMD ["./C-SFRAT"]
    ```

4. Build Your Custom Image

    In the same folder as your `C-SFRAT` executable, run this command once. This "bakes" the libraries into an image named `my-sfrat-app`.
    ```bash
    docker build -t my-sfrat-app .
    ```

5. Run the `C-SFRAT` tool

   In the same folder as your `C-SFRAT` executable, run the following command

   ```bash
   docker run --rm -e DISPLAY=host.docker.internal:0 -e LIBGL_ALWAYS_SOFTWARE=1 -v "$(pwd)":/app my-sfrat-app
   ```

   Note: Make sure XQuartz and Docker are running before running the command above.