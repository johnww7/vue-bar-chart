<template>
<div> 
    <svg></svg>
  
</div> 
</template>

<script>
import * as d3 from "d3";
import moment from "moment";
import { tip as d3tip } from "d3-v6-tip";


export default {
name: 'Chart',
  props: {
    chartData: Object
  },
  data () {
    return {
      chart: null,
    };
  },
  watch:  {
    chartData(val) {
      if (this.chart !== null) this.chart.remove();
      this.createChart(val)
    }
  },
  methods: {
    
    createChart(chartData) {
      const svg_width = 850;
      const svg_height = 750;

      let parseDate = d3.timeParse("%Y-%m-%d")
      
      console.log('component: ' + JSON.stringify(this.chartData.data));
      console.log('y max: ' + d3.max(chartData.data, (d)=> d[1]));

      let chartDateObj = [];
      chartData.data.forEach((e, i)=> {
        chartDateObj.push({
          date: parseDate(e[0]),
          gdp: e[1]
        });
        if(i == chartData.data.length-1){
          chartDateObj.push({
            date: parseDate("2019-01-01"),
            gdp: 0 
          });
        }
      });

      let dates = [];
      for (let obj of chartDateObj) {
        dates.push(obj.date);
      }

      const svg = d3
        .select("svg")
        .attr("width", svg_width)
        .attr("height", svg_height);

      const xScale = d3
        .scaleBand()
        .domain(chartDateObj.map(function(d) {return new Date(d.date)}))
        .range([40, svg_width])
        .padding(.2);
      
      const yScale = d3
        .scaleLinear()
        .domain([0, d3.max(chartDateObj, (d)=> d.gdp)])
        .range([svg_height-30, 0]);

      
      const xAxis = d3.axisBottom(xScale).tickValues(xScale.domain().filter(function(d,i) 
        {
          console.log('time: ' + moment(d).format("M YYYY") + ' ' + i);
          let year = moment(d).format('YYYY');
          let month = moment(d).format('M');
          return ((Number(year)%5 == 0 && month === '1'));
        }))
        .tickFormat(d3.timeFormat("%Y"));

      const yAxis = d3.axisLeft(yScale);

      var barTip = d3tip().attr("class", "d3Tip")
        .attr('id', 'tooltip')
        .offset([-10,0])
        .html(function(event, d) {
            let year = moment(d.date).format('YYYY');
            let month = moment(d.date).format('M');
            let quarter = moment(d.date).quarter();
            let gdpFormat = new Intl.NumberFormat('en-us', {style: 'currency', currency: 'USD'}).format(d.gdp)
            console.log('whats gdp: ' + d.gdp + ', year: ' + year + ', month: ' + month)
            return "<div style='background-color:#8d91a7'>" + year + ' Q' + quarter + "</br>" + gdpFormat + " Billion </div>"; 
        });

      this.chart = svg 
        .append("g")
        .attr("transform", "translate(0," + (svg_height-30) + ")")
        .attr("id", "x-axis")
        .attr("class", "tick")
        .style('font-size', "12px")
        .call(xAxis);
         
      this.chart = svg 
        .append("g")
        .attr("transform", "translate(" + 40 + ",0)")
        .attr("id", "y-axis")
        .attr("class", "tick")
        .style('font-size', "12px")
        .call(yAxis);

      
      this.chart.call(barTip);

      this.chart
        .selectAll("rect")
        .data(chartDateObj)
        .enter()
        .append("rect")
        .attr("fill", "blue")
        .attr("x", function(d) {return xScale(d.date) - 30})
        .attr("y", function(d) {return yScale(d.gdp)})
        .attr("height", (g) => (svg_height-30) - yScale(g.gdp))
        .attr("width", xScale.bandwidth())
        .on('mouseover', function(d,i) {
          barTip.show(d,i)
          d3.select(this)
            .attr("opacity", 0.1)
            .attr("x", function() {return xScale(i.date) - 30})
            .attr("y", function() {return yScale(i.gdp)})      
        })
       .on('mouseout', function(d,i) {
          barTip.hide(d,i)
          d3.select(this)
            .attr("opacity", 1)
            .attr("x", function() {return xScale(i.date) - 30})
            .attr("y", function() {return yScale(i.gdp)})
        });
        
        svg.append('text')
          .attr('x', -(svg_height/2))
          .attr('y', 80)
          .attr('transform', 'rotate(-90)')
          .attr('text-anchor', 'middle')
          .style('font-size', '25px')
          .text('Gross Domestic Product')

        svg.append('text')
          .attr('x', svg_width/2 +20)
          .attr('y', 80)
          .attr('text-anchor', 'middle')
          .style('font-size', '40px')
          .text('United States GDP')  
    },
    
  }   
}
</script>

<style scoped>
  .tick {
    color: blue;
    border: 20px red;
    font-size: 50px;
  }

  .bar {
    fill: blue;
  }
 

  .d3Tip{
  line-height: 10;
  font-weight: bold;
  padding: 12px;
  background-color: red;
  color: black;
  width: 100px;
  height: 50px;
  
}

.tip {
  line-height: 10;
  font-weight: bold;
  padding: 12px;
  background: red;
  color: black;
  width: 100px;
  height: 50px;
  
}

</style>