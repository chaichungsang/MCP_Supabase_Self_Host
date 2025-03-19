MCP_Supabase_Self_Host
======================

Host Your Own MCP in macOS to Query Supabase
--------------------------------------------

This guide explains how to set up a local instance of the Model Context Protocol (MCP) server to query your Supabase PostgreSQL database on macOS using Docker.

Prerequisites:
--------------
- A Supabase account with your project credentials (database password, project ID, and connection details).
- Docker Desktop for macOS installed (https://www.docker.com/products/docker-desktop/).

Step 1: Install Docker
----------------------
Download and install Docker Desktop for macOS by following the instructions on Docker’s website.

Step 2: Create a docker-compose.yml File
-----------------------------------------
1. Open your favorite text editor.
2. Create a new file named "docker-compose.yml".
3. Copy and paste the following content into the file:

--------------------------------------------------
version: '3.8'
services:
  db:
    image: supabase/postgres:15.8.1.049
    container_name: supabase-db
    restart: unless-stopped
    environment:
      POSTGRES_PASSWORD: your_postgres_password
      POSTGRES_DB: your_database_name
    ports:
      - "5432:5432"
    volumes:
      - db-data:/var/lib/postgresql/data

  mcp:
    image: node:18
    container_name: mcp-supabase
    working_dir: /app
    # This command runs the MCP server using npx.
    # Replace the connection string values with your own credentials.
    command: >
      sh -c "npx -y @modelcontextprotocol/server-postgres 'postgres://postgres:your_postgres_password@db:5432/your_database_name'"
    depends_on:
      - db
    ports:
      - "3000:3000"
    environment:
      NODE_ENV: production

volumes:
  db-data:
--------------------------------------------------

Note:
- Replace "your_postgres_password" with your actual PostgreSQL password.
- Replace "your_database_name" with your actual database name.

This file defines two services:
- **db**: Runs the official Supabase PostgreSQL container. It exposes port 5432 and persists data using a Docker volume named "db-data".
- **mcp**: Uses a Node 18 container to run the MCP server via npx. It connects to the PostgreSQL container with the given connection string and is exposed on port 3000.

Step 3: Configure Your Environment
------------------------------------
Make sure that your Supabase connection details (e.g., database password and database name) are correctly updated in the docker-compose.yml file.
You can also use a separate .env file to manage these variables if desired.

Step 4: Start the Docker Containers
-------------------------------------
1. Open Terminal.
2. Navigate to the directory containing your docker-compose.yml file.
3. Run the following command to start the containers in detached mode:

   docker-compose up -d

This command pulls the necessary images (if not already available), creates the containers, and starts them in the background.

Step 5: Verify the Services
---------------------------
To check that everything is running properly, execute:

   docker-compose ps

You should see both "supabase-db" and "mcp-supabase" containers with a status of "Up". To troubleshoot any issues, view the logs with:

   docker-compose logs -f

Step 6: Interact with Your MCP Server
---------------------------------------
Once the MCP server is running, access it via your web browser or API client at:

   http://localhost:3000

From here, you can use natural language queries to interact with your Supabase database. This endpoint can also be integrated with any MCP-compatible AI tool to perform SQL operations.

Conclusion
----------
You have successfully set up a self-hosted MCP server on macOS to query your Supabase PostgreSQL database. This setup can be extended or integrated with other services in your development environment, enabling you to experiment with natural language queries and build custom integrations.

Happy coding!
