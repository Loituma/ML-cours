<template>
  <div id="all">
    <h1> Dijkstra</h1>
    <div id="container"></div>
  </div>
</template>

<script>
import * as Three from 'three';

export default {
  name: 'Dijkstra',
  data() {
    return {
      scene: undefined,
      camera: undefined,
      renderer: undefined
    }
  },
  methods: {
    init: function () {
      let container = document.getElementById('container');
      let tailleTab = 16
      this.camera = new Three.PerspectiveCamera(70, container.clientWidth / container.clientHeight, 0.01, 20);
      this.camera.position.z = 15;
      this.camera.position.x = tailleTab / 2;
      this.camera.position.y = tailleTab / 2;

      this.scene = new Three.Scene();
      let tab = []
      let tile = []

      const geometry = new Three.PlaneGeometry(1, 1);
      const terre = new Three.MeshBasicMaterial({color: 0x32CD32, side: Three.DoubleSide});
      const lave = new Three.MeshBasicMaterial({color: 0xed0000, side: Three.DoubleSide});
      //generation map
      for (let i = 0; i < tailleTab; i++) {
        tab[i] = [];
        tile[i] = [];
        for (let j = 0; j < tailleTab; j++) {
          let mat = terre
          tab[i][j] = "terre";
          if (Math.floor(Math.random() * 3) == 0) {
            mat = lave
            tab[i][j] = "lave";
          }
          tile[i][j] = new Three.Mesh(geometry, mat)
          this.scene.add(tile[i][j]);
          // console.log(i + "  " +j)
          tile[i][j].position.x = i
          tile[i][j].position.y = j
        }
      }

      let xkey = Math.floor(Math.random() * tailleTab)
      let ykey = Math.floor(Math.random() * tailleTab)
      tab[xkey][ykey] = "key"
      tile[xkey][ykey].material = new Three.MeshBasicMaterial({color: 0xffff00, side: Three.DoubleSide});
      //generation hero
      let x = xkey
      let y = ykey
      while (x == xkey && y == ykey) {
        x = Math.floor(Math.random() * tailleTab)
        y = Math.floor(Math.random() * tailleTab)
      }
      tab[x][y] = "Hero"
      tile[x][y].material = new Three.MeshBasicMaterial({color: 0xf4fefe, side: Three.DoubleSide});
      // console.log(tab);

      //Gauche: x=-1 y=0
      //Haut: x=0 y=1
      //Droite: x=1 y=0
      //Bas: x=0 y=-1

      function makePath(x, y) {
        for (let dir = 0; dir < 4; dir++) {
          let newX = x;
          let newY = y;

          //Droite
          if (dir === 0 && newX < tailleTab-1) {
            newX += 1;
          }
          //Gauche
          if (dir === 1 && newX > 0) {
            newX -= 1;
          }
          //Haut
          if (dir === 2 && newY < tailleTab-1) {
            newY += 1;
          }
          //Bas
          if (dir === 3 && newY > 0) {
            newY -= 1;
          }

          if (!pathMake[newX]) {
            pathMake[newX] = [];
          }
          if (pathMake[newX][newY] === 'visited') {
            delete pathMake[newX][newY];
            return;
          }
          if (tab[newX][newY] === 'key') { //Verifie si la case est la "key"
            pathMake[newX][newY] = 'found';
            return;
          }
          if (tab[newX][newY] === 'terre') { //Verifie si la case est de la "terre"
            pathMake[newX][newY] = 'visited';
            tile[newX][newY].material = new Three.MeshBasicMaterial({color: 0xb452cd, side: Three.DoubleSide});
          }
        }
      }

      let pathMake = [];
      let isKeyFound = false;
      let countIteration = 0;
      makePath(x, y);

      while (!isKeyFound && countIteration < 5000) {
        pathMake.forEach((elemX, indexX) => {
          pathMake[indexX].forEach((elemY, indexY) => {
              countIteration++;

              if (pathMake[indexX][indexY] === 'found') {
                isKeyFound = true;
                return;
              }
              if (pathMake[indexX][indexY] === 'visited') makePath(indexX, indexY)
          })
        })
      }

      console.log(isKeyFound ? 'trouvé' : 'pas trouvé');
      console.log('clé en ', xkey, ykey, ' trouvé en ', countIteration, ' itérations');


//------------------------------------------------------------------------------------------------------------------
      this.renderer = new Three.WebGLRenderer({antialias: true});
      this.renderer.setSize(container.clientWidth, container.clientHeight);
      container.appendChild(this.renderer.domElement);

    },
    animate: function () {
      requestAnimationFrame(this.animate);
      //this.mesh.rotation.x += 0.01;
      //this.mesh.rotation.y += 0.02;
      this.renderer.render(this.scene, this.camera);
    },


  },
  mounted() {
    this.init();
    this.animate();
  }

}
</script>

<style scoped>
#container {
  width: 1000px;
  height: 700px;
  margin-left: auto;
  margin-right: auto;
}

</style>
