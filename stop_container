if [[ $(docker container ls -q --filter name=flask*) ]]; then
    docker container stop `$(docker container ls -q --filter name=flask*)`
else
    echo "no container running"
fi