
26/01/2025 -- legacy-dockerise-raw-4.md

— - - - - — - - - - - - - - - - - - - - — - - - - - - — - — - - - - - — - - - - — - - -
docker-compose build

docker ps

CONTAINER ID   IMAGE          COMMAND       CREATED          STATUS          PORTS                    NAMES
e7acd2c31847   a40e5cee3663   "/bin/bash"   39 minutes ago   Up 39 minutes   0.0.0.0:3000->3000/tcp   objective_yonath
docker stop e7acd2c31847
e7acd2c31847

Docker stop 
docker run -it --rm -p 3000:3000 inn-command-center-app /bin/bash

rails server --binding=0.0.0.0 --port=3000

— - - - - — - - - - - - - - - - - - - - — - - - - - - — - — - - - - - — - - - - — - - -
Workflow for Changes
1. Code-Only Changes:
    * Simply save your changes locally; they will reflect immediately.
2. Dependency or Configuration Changes:
    * Run:bash CopyEdit   docker-compose up --build
    *   
3. Debugging Rails:
    * Attach to the running container:bash CopyEdit   docker exec -it inn-command-center-app-1  /bin/bash
    *   
4. Restart Rails Server:
    * If necessary, restart only the Rails server without rebuilding:bash CopyEdit   bundle exec rails restart
    *   

Let me know if you need further adjustments!

4o




