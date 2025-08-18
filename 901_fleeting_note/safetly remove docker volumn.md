[[docker_idx]]


---

```sh

# list all Docker volumes to see what is currently available
docker volume ls


# check used size
docker system df -v


# lists volumes that are not used by any container:
docker volume ls -qf dangling=true


# delete orphaned volumes, you can add -f to force to add without asking
docker volume prune



# check only one
docker volume inspect YOUR_VOLUME_NAME

# remove only one
docker volume rm YOUR_VOLUME_NAME



```




 # script to automatically identify and delete orphaned volumes:
```sh

#!/bin/bash

# List all orphaned volumes
orphaned_volumes=$(docker volume ls -qf dangling=true)

if [ -z "$orphaned_volumes" ]; then
	echo "No orphaned volumes found."
else
	# Print a list of orphaned volumes
	echo "Orphaned volumes:"
	echo "$orphaned_volumes"
	# Ask for confirmation
	read -p "Do you want to remove these orphaned volumes? (y/n) " -n 1 -r
	echo

	if [[ $REPLY =~ ^[Yy]$ ]]; then
		# Remove orphaned volumes
		docker volume prune -f
		echo "Orphaned volumes removed."
	else
		echo "Operation canceled."
	fi
fi

```

Save this script as `cleanup_orphaned_volumes.sh`, make it executable, and run it:

```sh

chmod +x cleanup_orphaned_volumes.sh
./cleanup_orphaned_volumes.sh

```

