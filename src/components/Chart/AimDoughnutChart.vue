<template>
  <div>
    <canvas :id="cssId" width="133" height="114"></canvas>
  </div>
</template>

<script>
export default {
  props: {
    doughnutChartData: {
      type: Object,
      required: true
    }
  },
  mounted () {
    let min = 0
    let max = this.doughnutChartData.percentage
    setInterval(() => {
      if (min <= max) {
        min += 0.3
        this.drawCircle(min)
      }
    }, 0.01)
  },
  data () {
    return {
      cssId: this.doughnutChartData.cssId
    }
  },
  methods: {
    drawCircle (endpoint) {
      const canvas = document.getElementById(`${this.doughnutChartData.cssId}`)
      const centerX = canvas.width / 1.65
      const centerY = canvas.height / 1.6
      const ctx = canvas.getContext('2d')
      
      ctx.clearRect(0, 0, canvas.width, canvas.height)
      // Chart text
      ctx.font = '16.2px Helvetica'
      ctx.fillStyle = `${this.doughnutChartData.color}`
      ctx.fillText(this.doughnutChartData.title, 0, 15)
      ctx.font = '300 12px Helvetica'
      ctx.fillStyle = '#686868'
      ctx.fillText(this.doughnutChartData.subtitle1, 0, 36.2, 60)
      ctx.font = '300 10px Helvetica'
      ctx.fillStyle = '#686868'
      ctx.fillText(this.doughnutChartData.subtitle2, 
        // subtitle 2nd line position (x, y, maxwidth)
        this.doughnutChartData.subtitle2Position[0],
        this.doughnutChartData.subtitle2Position[1],
        this.doughnutChartData.subtitle2Position[2])
      ctx.font = '300 10px Helvetica'
      ctx.fillStyle = '#686868'
      ctx.fillText(this.doughnutChartData.subtitle3, 0, 49.2, 80)
      ctx.font = '20px Helvetica'
      ctx.fillStyle = '#686868'
      ctx.fillText(`${this.doughnutChartData.percentage}%`, 60, 68, 60)
      ctx.font = '300 12px Helvetica'
      ctx.fillStyle = '#686868'
      ctx.fillText(this.doughnutChartData.insideText, 55, 88, 60)
      // background
      ctx.beginPath()
      ctx.lineWidth = 8
      ctx.strokeStyle = '#D8D8D8'
      ctx.lineCap = 'round'
      ctx.arc(centerX, centerY, 38, 1.5 * Math.PI, 1 / 100 * 100 * Math.PI, false)
      ctx.stroke()
      ctx.fillStyle = 'rgb(255, 255, 255, 0)'
      ctx.fill()
      // color
      ctx.beginPath()
      ctx.lineWidth = 8
      ctx.strokeStyle = `${this.doughnutChartData.color}`
      ctx.lineCap = 'round'
      const fraction = endpoint / 100
      ctx.arc(centerX, centerY, 38, 1.5 * Math.PI, (1.0 + fraction) * 1.5 * Math.PI, false)
      ctx.stroke()
      ctx.fillStyle = 'rgb(255, 255, 255, 0)'
      ctx.fill()
    }
  },
  watch: {
    doughnutChartData: {
      handler: function (val) {
        this.drawCircle(
          val.cssId,
          val.title,
          val.subtitle1,
          val.subtitle2,
          val.subtitle2Position,
          val.subtitle3,
          val.percentage,
          val.insideText,
          val.color)
      },
      deep: true
    }    
  }
}
</script>

<style>
</style>