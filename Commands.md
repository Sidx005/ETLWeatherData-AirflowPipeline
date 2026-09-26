# Initialize project
astro dev init

# Start Airflow
astro dev start

# Stop Airflow
astro dev stop

# Restart Airflow
astro dev restart

# Check containers
docker ps

# Check Astro ports
docker ps --format "table {{.Names}}\t{{.Ports}}"

# Clean Astro environment if metadata/database gets corrupted
docker compose -p etl-weather_e79bb4 down --volumes --remove-orphans

# Start fresh
astro dev start
