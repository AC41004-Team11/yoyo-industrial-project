<template>
    <div class="container">
        <br/>
        <div class="Chart">
            <h1 style="text-align:center;">Sales Over Time</h1>
            <div class="row">
                <div  v-if="stores.length > 0" class="col-md-12">
                    <label>Store Selector</label>
                    <multiselect
                            v-model="storeChoice"
                            :options="stores"
                            :show-labels="false"
                            :close-on-select="false"
                            :hide-selected="true"
                            :multiple="true">
                    </multiselect>
                </div>
            </div>
            <div class="row">
                <div class="col-md-4">
                    <label>Compare by</label>
                    <multiselect
                            v-model="dateRangeChoice"
                            :options="dateRangeChoices"
                            :show-labels="false"
                            :close-on-select="false"
                            :hide-selected="true">
                    </multiselect>
                </div>
            </div>
            <div class="row">
                <div class="col-md-4">
                    <button v-on:click="getStores()">Get Data</button>
                </div>
            </div>
            <Chart :chart-data="datacollection" :options="options"></Chart>
        </div>
    </div>
</template>

<script>

    import Chart from './linechart';
    import Multiselect from 'vue-multiselect';
    import datePicker from 'vue-bootstrap-datetimepicker';
    import moment from 'moment';
    import 'eonasdan-bootstrap-datetimepicker/build/css/bootstrap-datetimepicker.css';


    export default {
        components: {
            'Chart': Chart,
            Multiselect,
            datePicker
        },

        props:['stores'],

        data() {
            return {
                configs: {
                    range: {
                        format: 'DD-MM-YYYY',
                        useCurrent: false,
                        showClear: true,
                        showClose: true,
                    }
                },
                showChart: false,
                dateRangeChoice: 'Week',
                dateRangeChoices: ['Week', 'Month', 'Year'],

                storeChoice: null,

                OriginDate:
                    {
                        startDate: null,
                        endDate: null,
                    },

                CompareDate:
                    {
                        startDate: null,
                        endDate: null,
                    },

                datacollection: {
                    labels: [],
                    datasets: [
                        {
                            label: null,
                            data: []
                        }
                    ]
                },

                options: {
                    bezierCurve : false,
                    responsive: true,
                    maintainAspectRatio: true,
                    scales: {
                        xAxes: [{
                            gridLines:
                                {
                                    display: true,
                                    borderDash: [8,4]
                                },
                            scaleLabel: {
                                display: true,
                                labelString: 'Date [Days]'
                            }
                        }],
                        yAxes: [{
                            gridLines:
                                {
                                    display: true,
                                    borderDash: [8,4]
                                },
                            scaleLabel: {
                                display: true,
                                labelString: 'Sales Amount [£]'
                            }
                        }]
                    }
                },

                graphData: [],
                dates: [],
            }
        },

        methods: {

            getDateRange()
            {
                var startDate = null;
                var endDate = null;

                var startDate2 = null;
                var endDate2 = null;

                if(this.dateRangeChoice == 'Week')
                {
                    this.OriginDate.startDate = moment().startOf("isoWeek");
                    this.OriginDate.endDate = moment().endOf("isoWeek");

                    startDate = this.OriginDate.startDate.get('year') + '-' + (this.OriginDate.startDate.get('month') + 1) + '-' +
                        this.OriginDate.startDate.get('date');
                    endDate = this.OriginDate.endDate.get('year') + '-' + (this.OriginDate.endDate.get('month')+1) +
                        '-' + this.OriginDate.endDate.get('date') + ' 23:59:59';

                    // subtract isnt actally deprecated.
                    // Moment docs say to use it.
                    this.CompareDate.startDate = moment().subtract(1, 'weeks').startOf("isoWeek");
                    this.CompareDate.endDate = moment().subtract(1, 'weeks').endOf("isoWeek");
                    startDate2 = this.CompareDate.startDate.get('year') + '-' + (this.CompareDate.startDate.get('month')+1) + '-' +
                        this.CompareDate.startDate.get('date');

                    endDate2 = this.CompareDate.endDate.get('year') + '-' +
                        (this.CompareDate.endDate.get('month')+1) + '-' +
                        this.CompareDate.endDate.get('date') + ' 23:59:59';

                    console.log(startDate + ' to ' + endDate);
                    console.log(startDate2 + ' to ' + endDate2);

                    return [startDate, endDate, startDate2, endDate2];
                }
                else if(this.dateRangeChoice == 'Month')
                {
                    this.OriginDate.startDate = moment().startOf("month");
                    this.OriginDate.endDate = moment().endOf("month");

                    startDate = this.OriginDate.startDate.get('year') + '-' + (this.OriginDate.startDate.get('month') + 1) + '-' +
                        this.OriginDate.startDate.get('date');
                    endDate = this.OriginDate.endDate.get('year') + '-' + (this.OriginDate.endDate.get('month')+1) +
                        '-' + this.OriginDate.endDate.get('date') + ' 23:59:59';

                    // subtract isnt actally deprecated.
                    // Moment docs say to use it.
                    this.CompareDate.startDate = moment().subtract(1, 'months').startOf("month");
                    this.CompareDate.endDate = moment().subtract(1, 'months').endOf("month");
                    startDate2 = this.CompareDate.startDate.get('year') + '-' +
                        (this.CompareDate.startDate.get('month')+1) + '-' +
                        this.CompareDate.startDate.get('date');

                    endDate2 = this.CompareDate.endDate.get('year') + '-' +
                        (this.CompareDate.endDate.get('month')+1) + '-' +
                        this.CompareDate.endDate.get('date') + ' 23:59:59';

                    console.log(startDate + ' to ' + endDate);
                    console.log(startDate2 + ' to ' + endDate2);
                    return [startDate, endDate, startDate2, endDate2];
                }
                else if(this.dateRangeChoice == 'Year')
                {
                    this.OriginDate.startDate = moment().startOf("year");
                    this.OriginDate.endDate = moment().endOf("year");

                     startDate = this.OriginDate.startDate.get('year') + '-' + (this.OriginDate.startDate.get('month') + 1) + '-' +
                         this.OriginDate.startDate.get('date');
                    endDate = this.OriginDate.endDate.get('year') + '-' + (this.OriginDate.endDate.get('month')+1) +
                        '-' + this.OriginDate.endDate.get('date') + ' 23:59:59';

                    // subtract isnt actally deprecated.
                    // Moment docs say to use it.
                    this.CompareDate.startDate = moment().subtract(1, 'years').startOf("year");
                    this.CompareDate.endDate = moment().subtract(1, 'years').endOf("year");
                    startDate2 = this.CompareDate.startDate.get('year') + '-' + (this.CompareDate.startDate.get('month')+1) + '-' +
                        this.CompareDate.startDate.get('date');

                    endDate2 = this.CompareDate.endDate.get('year') + '-' +
                        (this.CompareDate.endDate.get('month')+1) + '-' +
                        this.CompareDate.endDate.get('date') + ' 23:59:59';

                    console.log(startDate + ' to ' + endDate);
                    console.log(startDate2 + ' to ' + endDate2);
                    return [startDate, endDate, startDate2, endDate2];
                }
            },


            getStores()
            {
                this.graphData = [];
                var dateRange = this.getDateRange();

                this.dates = enumerateBetweenDates(this.OriginDate.startDate,
                    this.OriginDate.endDate, this.dateRangeChoice);


                if(this.storeChoice == null || this.storeChoice.length == 0)
                {
                    return;
                }

                var calls =[];

                for(var i = 0; i< this.storeChoice.length; i++) {
                    var address = ('/api/stores/' + this.storeChoice[i] +'/total-sales-value/' +
                        dateRange[0] + '/'
                        + dateRange[1]);
                    var prev = ('/api/stores/' + this.storeChoice[i] +'/total-sales-value/' +
                        dateRange[2] + '/'
                        + dateRange[3]);

                    calls.push(axios.get(address));
                    calls.push(axios.get(prev));
                }

                var data = [];

                axios.all(calls).then(function(results) {
                    results.forEach(function (response) {
                        data.push(response.data);
                    })
                }).then( response=>
                {
                    console.log(data);
                    this.checkData(data);

                });
            },

            checkData(data)
            {
                console.log(data.length);
                if(data.length < 1)
                {
                    return;
                }

                for(var i = 0; i < data.length; i++)
                {
                    var dataSet = [];
                    dataSet.name = null;
                    dataSet.colour = null;
                    dataSet.date = [];
                    dataSet.total = [];

                    dataSet.name = data[i][0].store_name;
                    dataSet.colour = data[i][0].store_colour;
                    dataSet.compare = false;

                    if(i % 2 != 0)
                    {
                        dataSet.name = dataSet.name + " (Last " + this.dateRangeChoice + ")";
                        dataSet.colour = '#'+Math.floor(Math.random()*16777215).toString(16);
                        dataSet.compare = true;
                    }
                    else
                    {
                        dataSet.name = data[i][0].store_name + "(This " + this.dateRangeChoice + ")";
                    }


                    for(var j= 0; j < data[i].length; j++)
                    {
                        dataSet.date.push(data[i][j].date);
                        dataSet.total.push(data[i][j].transaction_total_amount);
                    }
                    this.sortData(dataSet);
                }

                this.displayData();
            },

            sortData(dataSet)
            {

                // split up values of this dataset by date.
                var splitData = [];

                for(var i =0; i < this.dates.length; i++)
                {
                    splitData.push(0);
                }

                for(var i =0; i < dataSet.total.length; i++)
                {
                    if(dataSet.compare === false) {
                        for (var j = 0; j < this.dates.length; j++) {
                            if (moment(dataSet.date[i]).isBefore(this.dates[j])) {
                                splitData[j] += Math.round(parseFloat(dataSet.total[i]) * 100) / 100;
                                break;
                            }
                        }
                    }
                    else{
                        var dates = enumerateBetweenDates(this.CompareDate.startDate,
                            this.CompareDate.endDate, this.dateRangeChoice);

                        for (var j = 0; j < dates.length; j++) {
                            if (moment(dataSet.date[i]).isBefore(dates[j])) {
                                splitData[j] += Math.round(parseFloat(dataSet.total[i]) * 100) / 100;
                                break;
                            }
                        }
                    }
                }

                this.graphData.push({
                    label: dataSet.name,
                    borderColor: dataSet.colour,
                    backgroundColor: 'rgba(0,0,0,0)',
                    data:  splitData
                })
            },

            displayData()
            {
                var displayDates = [];

                for(var x = 0; x < this.dates.length; x++)
                {
                    var date = this.dates[x].substring(8, 10) + "-" + this.dates[x].substring(5, 7) + "-" +
                        this.dates[x].substring(0, 4);
                    displayDates.push(date);
                }

                console.log(displayDates);

                this.datacollection =
                    {
                        labels: displayDates,
                        datasets : this.graphData
                    }
                    this.showChart = true;
            },
        }
    }

    var enumerateBetweenDates = function(startDate, endDate, type) {
        var now = startDate;
        now.add(23, "hours");
        now.add(59, "minutes");
        now.add(59, "seconds");

        var dates = [];

        while (now.isBefore(endDate) || now.isSame(endDate)) {

            dates.push(now.format('YYYY-MM-DD HH:mm:ss'));
            if(type == "Week") {
                now.add(1, 'days');
            }
            if(type == "Month")
            {
                now.add(1, "weeks");
            }
            if(type == "Year")
            {
                now.add(1, "months");
            }
        }
        return dates;
    };
</script>

<style src="vue-multiselect/dist/vue-multiselect.min.css"></style>

<style>
    h1 {
        font-family: 'Helvetica', Arial;
        color: #464646;
        text-transform: uppercase;
        border-bottom: 1px solid #f1f1f1;
        padding-bottom: 15px;
        font-size: 28px;
        margin-top: 0;
    }

    .Chart {
        padding: 20px;
        box-shadow: 0px 0px 20px 2px rgba(0, 0, 0, .4);
        border-radius: 20px;
        margin: 50px 0;
    }
</style>