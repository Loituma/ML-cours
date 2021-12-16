
<template>
  <div id="all">
    <h1> Connect 4</h1>
    <ul>
      <li v-on:click="test(0)">Col 1</li>
      <li v-on:click="test(1)">Col 2</li>
      <li v-on:click="test(2)">Col 3</li>
      <li v-on:click="test(3)">Col 4</li>
      <li v-on:click="test(4)">Col 5</li>
      <li v-on:click="test(5)">Col 6</li>
      <li v-on:click="test(6)">Col 7</li>
    </ul>
    <div id="container"></div>
  </div>
</template>
<script>



import * as Three from 'three'
export default {
  name: 'Dijkstra',
  data () {
    return {
      scene: undefined,
      camera: undefined,
      renderer: undefined,
      tab: null,
      tile: null
    }
  },
  methods: {
    test: function (col) {
      let tailleTabx = 7
      let tailleTaby = 6

      for (let i = 0; i < tailleTabx; i++) {
        for (let j = 0; j < tailleTaby; j++) {
          if(i === col) {
            if(this.tab[i][j] === 0) {
              this.tab[i][j] = 1;
              this.tile[i][j].material = new Three.MeshBasicMaterial({color: 0x32CD32, side: Three.DoubleSide})
              this.playIA(this.tab, 2)
              return true;
            }
          }
        }
      }
    },
    playIA: function (tableau, player) {
      let tailleTabx = 7
      let tailleTaby = 6
      let tile = this.tile

        var posePlayer = [];
        for (let i = 0; i < tailleTabx; i++) {
          for (let j = 0; j < tailleTaby; j++) {
            if(tableau[i][j] === player) posePlayer.push({x: i, y: j})
          }
        }

        var poseAdverse = [];
        var nbAdverse = (player === 1) ? 2 : 1;
        for (let i = 0; i < tailleTabx; i++) {
          for (let j = 0; j < tailleTaby; j++) {
            if(tableau[i][j] === nbAdverse) poseAdverse.push({x: i, y: j})
          }
        }

        let checkVictoire1 = checkWin(tableau)
        if(checkVictoire1['bool']) {
          alert('Victoire player '+ checkVictoire1['player'])
          return true;
        }

        //Premier coup
        if(poseAdverse.length === 1) {
          const randomGD = Math.floor(Math.random() * 2);
          // const randomGD = 1;
          if((randomGD === 0 && poseAdverse[0]['x'] !== 0)
            || (randomGD === 1 && poseAdverse[0]['x'] === 6)) { //play left & check if border
            play(tableau, player, poseAdverse[0]['x'] - 1)
          } else { //play right
            play(tableau, player, poseAdverse[0]['x'] + 1)
          }
        } else {
          var trioDiago = [];
          var trio = [];
          var duo = [];
          var duodiago = [];
          var solo = [];
          for (var l=0; l < poseAdverse.length; l++) {
            //Verif ligne
            if(poseAdverse[l]['x'] < 6 && poseAdverse[l]['x'] > 0
              && tableau[poseAdverse[l]['x']-1][poseAdverse[l]['y']] === nbAdverse
              && tableau[poseAdverse[l]['x']+1][poseAdverse[l]['y']] === nbAdverse) {
              trio.push({x: (poseAdverse[l]['x']), y: poseAdverse[l]['y']})
            } else if(poseAdverse[l]['x'] > 0
              && tableau[poseAdverse[l]['x']-1][poseAdverse[l]['y']] === nbAdverse) {
              duo.push({x: (poseAdverse[l]['x']-1), y: poseAdverse[l]['y']})
            } else if(poseAdverse[l]['x'] < 6
              && tableau[poseAdverse[l]['x']+1][poseAdverse[l]['y']] === nbAdverse) {
              duo.push({x: (poseAdverse[l]['x']+1), y: poseAdverse[l]['y']})
            } else {
              solo.push({x: (poseAdverse[l]['x']), y: poseAdverse[l]['y']})
            }

            //Verif colonne
            if(poseAdverse[l]['y'] < 5
              && tableau[poseAdverse[l]['x']][poseAdverse[l]['y']-1] === nbAdverse
              && tableau[poseAdverse[l]['x']][poseAdverse[l]['y']+1] === nbAdverse) {
              trio.push({x: (poseAdverse[l]['x']), y: poseAdverse[l]['y']})
            } else if(poseAdverse[l]['y'] < 5
              && tableau[poseAdverse[l]['x']][poseAdverse[l]['y']-1] === nbAdverse) {
              duo.push({x: (poseAdverse[l]['x']), y: poseAdverse[l]['y']-1})
            } else if(poseAdverse[l]['y'] < 5
              && tableau[poseAdverse[l]['x']][poseAdverse[l]['y']+1] === nbAdverse) {
              duo.push({x: (poseAdverse[l]['x']), y: poseAdverse[l]['y']+1})
            } else {
              solo.push({x: (poseAdverse[l]['x']), y: poseAdverse[l]['y']})
            }

            // //Verif diago droite
            if(poseAdverse[l]['x'] > 0 && poseAdverse[l]['x'] < 6
              && poseAdverse[l]['y'] > 0 && poseAdverse[l]['y'] < 5
              && tableau[poseAdverse[l]['x']+1][poseAdverse[l]['y']+1]
                && tableau[poseAdverse[l]['x']-1][poseAdverse[l]['y']-1] === nbAdverse) {
              trioDiago.push({x: (poseAdverse[l]['x']), y: poseAdverse[l]['y']})
            } else if(poseAdverse[l]['x'] > 0 && poseAdverse[l]['y'] > 0
              && tableau[poseAdverse[l]['x']-1][poseAdverse[l]['y']-1] === nbAdverse) { //bas gauche
              duodiago.push({x: (poseAdverse[l]['x']-1), y: poseAdverse[l]['y']-1})
            } else if(poseAdverse[l]['x'] < 6 && poseAdverse[l]['y'] < 6
              && tableau[poseAdverse[l]['x']+1][poseAdverse[l]['y']+1] === nbAdverse) { //haut droite
              duodiago.push({x: (poseAdverse[l]['x']+1), y: poseAdverse[l]['y']+1})
            }

            // //Verif diago gauche
            if(poseAdverse[l]['x'] > 0 && poseAdverse[l]['x'] < 6
              && poseAdverse[l]['y'] > 0 && poseAdverse[l]['y'] < 5
              && tableau[poseAdverse[l]['x']-1][poseAdverse[l]['y']+1]
              && tableau[poseAdverse[l]['x']+1][poseAdverse[l]['y']-1] === nbAdverse) {
              trioDiago.push({x: (poseAdverse[l]['x']), y: poseAdverse[l]['y']})
            } else if(poseAdverse[l]['x'] > 0 && poseAdverse[l]['y'] < 6
              && tableau[poseAdverse[l]['x']-1][poseAdverse[l]['y']+1] === nbAdverse) { //haut gauche
              duodiago.push({x: (poseAdverse[l]['x']-1), y: poseAdverse[l]['y']+1})
            } else if(poseAdverse[l]['x'] < 6 && poseAdverse[l]['y'] > 0
              && tableau[poseAdverse[l]['x']+1][poseAdverse[l]['y']-1] === nbAdverse) { // bas droite
              duodiago.push({x: (poseAdverse[l]['x']+1), y: poseAdverse[l]['y']-1})
            }
          }//End for



          //==============
          //Place IA
          //==============
          if(trioDiago.length > 0) {
            for (var lTrioDiago=0; lTrioDiago < trioDiago.length; lTrioDiago++) {
              if(trioDiago[lTrioDiago]['x'] < 5 && trioDiago[lTrioDiago]['y'] < 4
                && tableau[trioDiago[lTrioDiago]['x']+2][trioDiago[lTrioDiago]['y']+2] === 0
                && tableau[trioDiago[lTrioDiago]['x']+2][trioDiago[lTrioDiago]['y']+1] !== 0) {//Haut droite
                play(tableau, player, trioDiago[lTrioDiago]['x']+2)
                return
              } else if(trioDiago[lTrioDiago]['x'] > 1 && trioDiago[lTrioDiago]['y'] < 4
                && tableau[trioDiago[lTrioDiago]['x']-2][trioDiago[lTrioDiago]['y']+2] === 0
                && tableau[trioDiago[lTrioDiago]['x']-2][trioDiago[lTrioDiago]['y']+1] !== 0) {//Haut gauche
                play(tableau, player, trioDiago[lTrioDiago]['x']-2)
                return
              } else if(trioDiago[lTrioDiago]['x'] > 1 && trioDiago[lTrioDiago]['y'] >= 2
                && tableau[trioDiago[lTrioDiago]['x']-2][trioDiago[lTrioDiago]['y']-2] === 0
                && tableau[trioDiago[lTrioDiago]['x']-2][trioDiago[lTrioDiago]['y']-3] !== 0) {//Bas gauche
                play(tableau, player, trioDiago[lTrioDiago]['x']-2)
                return
              } else if(trioDiago[lTrioDiago]['x'] < 5 && trioDiago[lTrioDiago]['y'] >= 2
                && tableau[trioDiago[lTrioDiago]['x']+2][trioDiago[lTrioDiago]['y']-2] === 0
                && tableau[trioDiago[lTrioDiago]['x']+2][trioDiago[lTrioDiago]['y']-3] !== 0) {//Bas droite
                play(tableau, player, trioDiago[lTrioDiago]['x']+2)
                return
              }
            }
          }


          if(trio.length > 0) {
            for (var lTrio=0; lTrio < trio.length; lTrio++) {
              if(trio[lTrio]['y'] < 5
                && tableau[trio[lTrio]['x']][trio[lTrio]['y']+2] === 0 && tableau[trio[lTrio]['x']][trio[lTrio]['y']+1] === nbAdverse
                && tableau[trio[lTrio]['x']][trio[lTrio]['y']+3] !== player) { //haut
                play(tableau, player, trio[lTrio]['x'])
                return
              } else if(trio[lTrio]['y'] < 5 && trio[lTrio]['x'] > 1
                && tableau[trio[lTrio]['x']-2][trio[lTrio]['y']] === 0
                && tableau[trio[lTrio]['x']-2][trio[lTrio]['y']] !== undefined
                && tableau[trio[lTrio]['x']-2][trio[lTrio]['y']-1] !== 0) { //gauche
                play(tableau, player, trio[lTrio]['x']-2)
                return
              } else if(trio[lTrio]['y'] < 5 && trio[lTrio]['x'] < 5
                && tableau[trio[lTrio]['x']+2][trio[lTrio]['y']] === 0
                && tableau[trio[lTrio]['x']+2][trio[lTrio]['y']] !== undefined
                && tableau[trio[lTrio]['x']+2][trio[lTrio]['y']-1] !== 0) { //Droite
                play(tableau, player, trio[lTrio]['x']+2)
                return
              }
            }
          }

          if(duo.length > 0) {
            for (var lDuo=0; lDuo < duo.length; lDuo++) {
              if(duo[lDuo]['x'] > 0
                && tableau[duo[lDuo]['x']-1][duo[lDuo]['y']] === 0
                && tableau[duo[lDuo]['x']-1][duo[lDuo]['y']] !== undefined
                && tableau[duo[lDuo]['x']-1][duo[lDuo]['y']-1] !== 0) { //gauche
                play(tableau, player, duo[lDuo]['x']-1)
                return
              } else if(duo[lDuo]['x'] < 6
                && tableau[duo[lDuo]['x']+1][duo[lDuo]['y']] === 0
                && tableau[duo[lDuo]['x']+1][duo[lDuo]['y']] !== undefined
                && tableau[duo[lDuo]['x']+1][duo[lDuo]['y']-1] !== 0) { //Droite
                play(tableau, player, duo[lDuo]['x']+1)
                return
              } else if(tableau[duo[lDuo]['x']][duo[lDuo]['y']+1] === 0) { //haut
                play(tableau, player, duo[lDuo]['x'])
                return
              }
            }
          }

          if(duodiago.length > 0) {
            for (var lDuoDiago=0; lDuoDiago < duodiago.length; lDuoDiago++) {
              if(duodiago[lDuoDiago]['x'] < 5 && duodiago[lDuoDiago]['y'] > 0
                && tableau[duodiago[lDuoDiago]['x']+1][duodiago[lDuoDiago]['y']+1] === 0
                && tableau[duodiago[lDuoDiago]['x']+1][duodiago[lDuoDiago]['y']] !== 0) {//Haut droite
                play(tableau, player, duodiago[lDuoDiago]['x']+2)
                return
              } else if(duodiago[lDuoDiago]['x'] > 1 && duodiago[lDuoDiago]['y'] < 5
                && tableau[duodiago[lDuoDiago]['x']-1][duodiago[lDuoDiago]['y']+1] === 0
                && tableau[duodiago[lDuoDiago]['x']-1][duodiago[lDuoDiago]['y']] !== 0) {//Haut gauche
                play(tableau, player, duodiago[lDuoDiago]['x']-2)
                return
              } else if(duodiago[lDuoDiago]['x'] > 1 && duodiago[lDuoDiago]['y'] < 4
                && tableau[duodiago[lDuoDiago]['x']-1][duodiago[lDuoDiago]['y']-1] === 0
                && tableau[duodiago[lDuoDiago]['x']-1][duodiago[lDuoDiago]['y']-2] !== 0) {//Bas gauche
                play(tableau, player, duodiago[lDuoDiago]['x']-2)
                return
              } else if(duodiago[lDuoDiago]['x'] < 5 && duodiago[lDuoDiago]['y'] > 1
                && tableau[duodiago[lDuoDiago]['x']+1][duodiago[lDuoDiago]['y']-1] === 0
                && tableau[duodiago[lDuoDiago]['x']+1][duodiago[lDuoDiago]['y']-2] !== 0) {//Bas droite
                play(tableau, player, duodiago[lDuoDiago]['x']+2)
                return
              }
            }
          }

          if(solo.length > 0) {
            for (var lSolo=0; lSolo < solo.length; lSolo++) {
              if(solo[lSolo]['x'] > 1
                && tableau[solo[lSolo]['x']-1][solo[lSolo]['y']] === 0) { //gauche
                play(tableau, player, solo[lSolo]['x']-1)
                return
              } else if(solo[lSolo]['x'] < 6
                && tableau[solo[lSolo]['x']+1][solo[lSolo]['y']] === 0) { //Droite
                play(tableau, player, solo[lSolo]['x']+1)
                return
              } else if(solo[lSolo]['y'] < 5
                && tableau[solo[lSolo]['x']][solo[lSolo]['y']+1] === 0) { //haut
                play(tableau, player, solo[lSolo]['x'])
                return
              }
            }
          }
        }

        function play(tableau, player, column) { //Placer le point
          for (let i = 0; i < tailleTabx; i++) {
            for (let j = 0; j < tailleTaby; j++) {
              if(i === column) {
                if(tableau[i][j] === 0) {
                  tableau[i][j] = player;
                  changeColor(i, j, player);
                  let checkVictoire2 = checkWin(tableau)
                  if(checkVictoire2['bool']) {
                    alert('Victoire player '+ checkVictoire2['player'])
                    return true;
                  }
                  return true;
                }
              }
            }
          }
        }

        function changeColor(x, y, player) { //Change couleur de la case
          if (player === 2) {
            tile[x][y].material = new Three.MeshBasicMaterial({color: 0xed0000, side: Three.DoubleSide})
          } else if (player === 1) {
            tile[x][y].material = new Three.MeshBasicMaterial({color: 0x32CD32, side: Three.DoubleSide})
          }
        }

        function checkWin(tableau) {
          for (let i = 0; i < tailleTabx; i++) {
            for (let j = 0; j < tailleTaby; j++) {
              if(tableau[i][j] !== 0 && i <=3
                && tableau[i+1][j] === tableau[i][j]
                && tableau[i+2][j] === tableau[i][j]
                && tableau[i+3][j] === tableau[i][j]) { // Ligne
                console.log(tableau[i][j])
                return {bool: true, player: tableau[i][j]};
              } else if(tableau[i][j] !== 0
                && tableau[i][j+1] === tableau[i][j]
                && tableau[i][j+2] === tableau[i][j]
                && tableau[i][j+3] === tableau[i][j]) { // Column
                return {bool: true, player: tableau[i][j]};
              } else if(tableau[i][j] !== 0 && i <= 3
                && tableau[i+1][j+1] === tableau[i][j]
                && tableau[i+2][j+2] === tableau[i][j]
                && tableau[i+3][j+3] === tableau[i][j]) { // Diago droite
                return {bool: true, player: tableau[i][j]};
              } else if(tableau[i][j] !== 0 && j >= 3 && i <= 3
                && tableau[i+1][j-1] === tableau[i][j]
                && tableau[i+2][j-2] === tableau[i][j]
                && tableau[i+3][j-3] === tableau[i][j]) { // Diago gauche
                return {bool: true, player: tableau[i][j]};
              }
            }//end for y
          }//end for x
          return {bool: false};
        }

        this.tab = tableau
        this.tile = tile

    },
    init: function () {
      let container = document.getElementById('container')
      let tailleTabx = 7
      let tailleTaby = 6
      this.camera = new Three.PerspectiveCamera(70, container.clientWidth / container.clientHeight, 0.01, 20)
      this.camera.position.z = 15
      this.camera.position.x = tailleTabx / 2
      this.camera.position.y = tailleTaby / 2

      this.scene = new Three.Scene()
      let tab = []
      let tile = []

      const geometry = new Three.PlaneGeometry(1, 1)
      const damier1 = new Three.MeshBasicMaterial({color: 0xd4d4d4, side: Three.DoubleSide})
      const damier2 = new Three.MeshBasicMaterial({color: 0xf0f0f0, side: Three.DoubleSide})

      // var geo = new Three.EdgesGeometry(geometry);
      // var mat = new Three.LineBasicMaterial( { color: 0x000000, linewidth: 4 } );
      // var wireframe = new Three.LineSegments( geo, mat );
      // wireframe.renderOrder = 1; // make sure wireframes are rendered 2nd
      // this.scene.add(wireframe)

      for (let i = 0; i < tailleTabx; i++) {
        tab[i] = []
        tile[i] = []
        for (let j = 0; j < tailleTaby; j++) {
          let mat = damier1
          tab[i][j] = 0
          if ((i % 2 === 0 && j % 2 === 1) || (i % 2 === 1 && j % 2 === 0)) {
            mat = damier2
          }
          tile[i][j] = new Three.Mesh(geometry, mat)
          this.scene.add(tile[i][j])

          tile[i][j].position.x = i
          tile[i][j].position.y = j
        }
      }

      this.tile = tile
      this.tab = tab
      // ------------------------------------------------------------------------------------------------------------------
      this.renderer = new Three.WebGLRenderer({antialias: true})
      this.renderer.setSize(container.clientWidth, container.clientHeight)
      container.appendChild(this.renderer.domElement)
    },
    animate: function () {
      requestAnimationFrame(this.animate)
      // this.mesh.rotation.x += 0.01;
      // this.mesh.rotation.y += 0.02;
      this.renderer.render(this.scene, this.camera)
    }

  },
  mounted () {
    this.init()
    this.animate()
  }

}
</script>

<style scoped>
  #container{
    width: 1000px;
    height: 700px;
    margin-left: auto;
    margin-right: auto;
  }

</style>
