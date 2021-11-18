Función bash/zsh para el forwardeo de puertos en una línea de terminal:

```bash
function fsshmap() {
  ### Codigo extraido y modificado de
  ### https://stackoverflow.com/questions/29936948/ssh-l-forward-multiple-ports
  line=""
  for i in ${@:2}
  do
    echo "Forwarding port $i to $1.datos.cluster.uy"
    line="$line -L localhost:$i:$1.datos.cluster.uy:$i"
  done
  cline="ssh -N $line cluster"
  # echo $cline
  eval $cline
}

functtion forwardcluster() {
  echo "   code-server "`ssh cluster cat /clusteruy/home/cmarino/.config/code-server/config.yaml | grep "password: "`
  echo
  fsshmap $1 {6000..6006} {8080..8082} {8888..8895}
}
```

Para utilizarle se recomienda agregar en el archivo .bashrz / .zshrc