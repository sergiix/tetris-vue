<template>
  <div id="app" v-on:keyup="keyDown()">
    <div>Score: {{score}}</div>
    <div class="table">
      <div v-for="row in table" class="row">
          <div 
            v-for="cell, i in row" 
            :style="{height: height + 'px', width: width + 'px'}" 
            :class="{filled: cell}" class="cell"></div>
        </div>
    </div>
  </div>
</template>

<script>
  let figures = [
    [
      [
        [1],
        [1],
        [1],
        [1]
      ],
      [
        [1,1,1,1]
      ]
    ],
    [
      [
        [1,0],
        [1,0],
        [1,1]
      ],
      [
        [1,1,1],
        [1,0,0],
      ],
      [
        [1,1],
        [0,1],
        [0,1],
      ],
      [
        [0,0,1],
        [1,1,1],
      ]
    ],
    [
      [
        [0,1,0],
        [1,1,1]
      ],
      [
        [1,0],
        [1,1],
        [1,0]
      ],
      [
        [1,1,1],
        [0,1,0]
      ],
      [
        [0,1],
        [1,1],
        [0,1]
      ]
    ],
    [
      [
        [1,1],
        [1,1]
      ]
    ],
    [
      [
        [1]
      ]
    ]
  ]

  export default {
    name: 'app',
    data() {
      return {
        figure: { x: 0, y: 0, data: {}, figureIndex: 0, variantIndex: 0 },
        table: [
          [0,0,0,0,0,0,0,0,0,0,0,0,0],
          [0,0,0,0,0,0,0,0,0,0,0,0,0],
          [0,0,0,0,0,0,0,0,0,0,0,0,0],
          [0,0,0,0,0,0,0,0,0,0,0,0,0],
          [0,0,0,0,0,0,0,0,0,0,0,0,0],
          [0,0,0,0,0,0,0,0,0,0,0,0,0],
          [0,0,0,0,0,0,0,0,0,0,0,0,0],
          [0,0,0,0,0,0,0,0,0,0,0,0,0],
          [0,0,0,0,0,0,0,0,0,0,0,0,0],
          [0,0,0,0,0,0,0,0,0,0,0,0,0],
          [0,0,0,0,0,0,0,0,0,0,0,0,0],
          [0,0,0,0,0,0,0,0,0,0,0,0,0],
          [0,0,0,0,0,0,0,0,0,0,0,0,0],
          [0,0,0,0,0,0,0,0,0,0,0,0,0],
          [0,0,0,0,0,0,0,0,0,0,0,0,0],
          [0,0,0,0,0,0,0,0,0,0,0,0,0],
          [0,0,0,0,0,0,0,0,0,0,0,0,0],
          [0,0,0,0,0,0,0,0,0,0,0,0,0],
          [0,0,0,0,0,0,0,0,0,0,0,0,0],
          [0,0,0,0,0,0,0,0,0,0,0,0,0],
          [0,0,0,0,0,0,0,0,0,0,0,0,0],
          [0,0,0,0,0,0,0,0,0,0,0,0,0],
          [0,0,0,0,0,0,0,0,0,0,0,0,0],
        ],
        rows: 15,
        height: 8,
        width: 8,
        score: 0
      }
    },
    created() {
      this.genFigure()
      this.printFigure()
      setInterval(() => this.keyDown(), 1000);
      addEventListener('keydown', (e) => {
        let code = e.keyCode || e.which;
        switch(code) {
          case 37: return this.keyLeft();
          case 38: return this.rotateFigure(); // Key.UP
          case 39: return this.keyRight();
          case 40: return this.keyDown();
        }
      })
    },
    computed: {
      tableHeight() {
        return Object.keys(this.table).length
      },
      tableWidth() {
        return this.table[0].length
      },
      figureHeight() {
        return this.figure.data.length
      },
      figureWidth() {
        return this.figure.data[0].length
      },
      x() {
        return this.figure.x
      },
      y() {
        return this.figure.y
      },
      data() {
        return this.figure.data
      },
      figureIndex() {
        return this.figure.figureIndex
      },
      figureVariantIndex() {
        return this.figure.variantIndex
      }
    },
    methods: {
      genFigure() {
        let figureIndex = Math.floor(Math.random() * figures.length);
        let variantIndex = 0
        this.figure = {
          x: parseInt(this.tableWidth/2-1, 10),
          y: 0,
          data: figures[figureIndex][variantIndex],
          figureIndex,
          variantIndex
        }
      },
      keyDown() {
        let figure = {...this.figure}
        figure.y++
        if (this.canMoveDown(figure)) {
            console.log({figure, thisFigure: this.figure})
            this.clearFigure(this.figure)
            this.figure = figure
        }
        else {
          this.checkScore()
          this.genFigure()
        }
        this.printFigure()
      },
      keyRight() {
        let figure = {...this.figure}
        figure.x++
        if (this.canMoveRight(figure)) {
            this.clearFigure(this.figure)
            this.figure = figure
        }
        this.printFigure()
      },
      keyLeft() {
        let figure = {...this.figure}
        figure.x--
        if (this.canMoveLeft(figure)) {
            this.clearFigure(this.figure)
            this.figure = figure
        }
        this.printFigure()
      },
      clearFigure(figure) {
        let {x, y, data} = figure
        let table = {...this.table}
        for (let iy = 0; iy < data.length; iy++) {
          for (let ix = 0; ix < data[iy].length; ix++) {
            table[y + iy][x + ix] = 0
          }
        }
        this.table = table;
      },
      printFigure() {
        let {figure} = this
        let {x, y, data} = figure
        let table = {...this.table}
        for (let iy = 0; iy < data.length; iy++) {
          for (let ix = 0; ix < data[iy].length; ix++) {
            table[y + iy][x + ix] = data[iy][ix]
          }
        }
        this.table = table;
      },
      canMoveLeft(figure) {
        let {x, y, data} = figure
        let {table} = this
        if (x < 0)
          return false;
        for (let i = 0; i < data.length; i++)
          if (table[y + i][x] == 1)
            return false
        return true
      },
      canMoveRight(figure) {
        let {x, y, data} = figure
        let {table, tableWidth, figureWidth} = this
        if (x + figureWidth > tableWidth)
          return false;
        for (let i = 0; i < data.length; i++)
          if (table[y+i][x + figureWidth] === 1)
            return false
        return true
      },
      canMoveDown() {
        let {x, y, data, figureHeight, tableHeight, table} = this
        let iy = y + figureHeight
        let ix = x
        if (y + figureHeight >= tableHeight)
          return false;
        for (let i = 0; i < data[figureHeight-1].length; i++)
          if (table[iy][x + i] == 1)
            return false
        return true
      },
      rotateFigure() {
        let { figure, table, figureIndex, figureVariantIndex, y, x, tableHeight, tableWidth } = this
        this.clearFigure(figure);
        figureVariantIndex = (figureVariantIndex + 1) % figures[figureIndex].length
        let data = [...figures[figureIndex][this.figure.variantIndex]]
        for(let iy = 0; iy < data.length; iy++) {
          for (let ix = 0; ix < data[iy].length; ix++) {
            if (y + iy >= tableHeight || x + ix >= tableWidth || table[y + iy][x + ix] === 1 && table[y + iy][x + ix] !== 0) {
              this.printFigure()
              return;
            } 
          }
        }
        this.figure.variantIndex = figureVariantIndex;
        this.figure.data = data
        this.printFigure()
      },
      checkScore() {
        let {table, tableWidth, tableHeight} = this
        for(let iy=0;iy<tableHeight;iy++) {
          let isRowFilled = true
          for(let ix=0;ix<tableWidth&&isRowFilled;ix++) {
            isRowFilled = table[iy][ix] == 1 && isRowFilled
          }
          if (isRowFilled)
            this.removeRow(iy)
        }
      },
      removeRow(y) {
        this.score++
        let {table, tableWidth, tableHeight} = this
        for(let i=0;i<tableWidth;i++) {
          table[y][i] = 0
          for(let iy=y; iy >= 0; iy--)
            table[iy][i] = iy-1 < 0 ? 0 : table[iy-1][i]
        }
        this.checkScore()
      }
    }
  }
</script>

<style>
  * {
    box-sizing: border-box;
  }
  .table {
    display: inline-block;
    border-left: 1px solid #c0b6c9;
    border-top: 1px solid #c0b6c9;
  }
  .row {
    display: flex;
  }
  .cell {
    display: flex;
    position: relative;
    background: #d9d3de;
    border-right: 1px solid #c0b6c9;
    border-bottom: 1px solid #c0b6c9;
  }
  .filled {
    background-color: #c0b6c9;
  }
</style>