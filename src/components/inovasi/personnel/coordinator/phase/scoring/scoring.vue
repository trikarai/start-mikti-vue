<script>
  import auth from '@/httpcontrol' 
  import Loading from '@/components/loading'
  import * as CONFIG from '@/app.config.js'
  import alertControl from '@/alertcontrol'
  import ErrorFlash from '../../../../errorflash'
    import Period from './periodForm'

  export default {
    template:require('./scoring.html'),
    data: function () {
      return {
        data : {total:0,list:[]},
        summaryData : [],
        success : false,
        error : false,
        err_msg: '',
        loading : false,
        onSubmit : false,
        showForm : false,
        page: {
          page: 1,
          size: 5
        },
        programmeId : this.$route.params.programmeId,
        phaseId : this.$route.params.phaseId,
        scoringData : '',
        showSummarySection : false
      }
    },
    components: {
      'error-flash': ErrorFlash,
      'loading': Loading,
      'period' : Period

    },
    created: function (){
      this.getscoring()
    },
    computed: {},
    mounted: function (){},
    update: function (){},
    methods: {
      getscoring: function (){
        auth.getData(this, '/programme/'+this.programmeId+'/phase/' + this.phaseId + '/scoring')
      },
      changePeriod: function (scoringData) {
//        alert(phaseId)
        this.scoringData = scoringData
        this.showForm = true
        this.showSummarySection = false
      },
      closeModalRefresh: function (){
        this.showForm = false
        this.showSummarySection = false
        this.getscoring()
      },
      summary: function (id){
        this.loading = true
        this.summaryData = []
        auth.getData2(this, '/programme/'+this.programmeId+'/phase/' + id + '/score_summary')
                .then(res=>{
                  if(res.status === 204){
                    this.summaryData = []
                    this.loading = false
                  }else{
                    return res.json()
                  }
                },error=>{
                  console.log(error)
                  alertControl.showError(this, error.body.meta)
                  this.loading = false
                }).then(data=>{
                  this.summaryData = data.data
                  this.showSummarySection = true
                  this.loading = false
                })
      }
    }
  }
  
</script>
