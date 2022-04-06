<template>
  <div class="px-5">
    <div>
        <b-nav class="d-flex justify-content-between">
            <b-nav-item >Logo</b-nav-item>
            <b-nav-item>Log out</b-nav-item>
        </b-nav>  
    </div>
    <h1>Admin Dashboard</h1>
    
    <b-nav tabs>
        <b-nav-item active>Candidate</b-nav-item>
        <b-nav-item>Jobs</b-nav-item>
        <b-nav-item>Clients</b-nav-item>
        <b-nav-item>Recruiters</b-nav-item>
    </b-nav>
    <div class="row d-flex justify-content-between">
        <div class="w-25"><b-form-input class="mt-3" type="text" v-model="searchValue" placeholder="Search" id="search-input"></b-form-input></div>
        <div class="w-50 float-right">
          
          <b-dropdown class="btn  m-3" variant="link" toggle-class="text-decoration-none" no-caret>
            <template #button-content>
              <b-button  variant="outline-secondary"><b-icon icon="building" ></b-icon>Client : {{selectedClient}}</b-button>
            </template>
            <b-dropdown-item @click="selectedClient = 'All'" >
         All</b-dropdown-item>
            <b-dropdown-item @click="selectedClient = candidate.client" v-for="candidate in candidates" :key="candidate.client">
         {{ candidate.client }}</b-dropdown-item>
          </b-dropdown>
          <b-button class="btn  m-3" variant="outline-secondary" id="toggle-btn" @click="showFilter"><b-icon icon="funnel-fill" ></b-icon>Add Filter</b-button>
        </div>
    </div>
    <table class="w-100">
      <tr>
        <th>Submision Date</th>
        <th>Name</th>
        <th>Job Title</th>
        <th>Client</th>
        <th>Recruiter</th>
        <th>Total Score</th>
        <th>Status</th>
        <th>Notes</th>
        <th>Action</th>
      </tr>
      <tr v-for="candidate in filteredcandidates" :key="candidate.name">
        <td>{{ candidate.submissionDate }}</td>
        <td>{{ candidate.name }}</td>
        <td>{{candidate.score}}%</td>
        <td>{{candidate.client}}</td>
        <td>{{candidate.recruiter}}</td>
        <td>{{candidate.score}}%</td>
        <td>{{candidate.status}}</td>
        <td>{{candidate.notes}}</td>
        <td></td>
      </tr>
    </table>
    <b-modal ref="filter-menu" hide-footer>
      <div>
        <p>Filter By</p>
        <div>
          <div class="row">
              Client : 
            <select name="client" class="w-25" id="select" v-model="selectedClient">
            <option value="All">All</option>
            <option  v-for="candidate in candidates" :key="candidate.client" >{{ candidate.client }}</option>
          </select>
          </div>
          <div class="row">
              Recruiter : 
            <select name="recruiter" class="w-25" id="select" v-model="selectedRecruiter">
            <option value="All">All</option>
            <option  v-for="candidate in candidates" :key="candidate.recruiter" >{{ candidate.recruiter }}</option>
          </select>
          </div>

          <div class="row">
              Status : 
            <select name="status" class="w-25" id="select" v-model="selectedStatus">
              <option value="All">All</option>
              <option value="Pending">Pending</option>
              <option value="Rejected">Rejected</option>
              <option value="Shortlisted">Shortlisted</option>
              <option value="Interview">Interview</option>
              <option value="Offer">Offer</option>
              <option value="Offer Rejected">Offer Rejected</option>
              <option value="Offer Accepted">Offer Accepted</option>
          </select>
          </div>

        <div class="row w-100">
          <input placeholder="Min. Score" class="w-25 m-1"  type="number" v-model="minScore" id="cooking-time-input"/>
          to
          <input placeholder="Max Score" class="w-25 m-1" type="number" v-model="maxScore" id="cooking-time-input"/>
        </div>
        <div class="row w-100">
          <input type="date" id="minDate" name="minDate" v-model="minDate" class="w-25 m-1" />
          to
          <input type="date" id="maxDate" name="maxDate" v-model="maxDate" class="w-25 m-1" />
        </div>
      </div>
      </div>
    </b-modal>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        text: 'Test',
         ascending: true,
      sortBy: 'alphabetically',
      searchValue: '',
      selectedClient: 'All',
      selectedRecruiter: 'All',
      selectedStatus: 'All',
      maxScore: null,
      minScore: null,
    candidates: [
    {name: 'Nik', submissionDate: '19/2/2022',  score: 60, client: 'JEV', recruiter: 'Fredrik', status: 'Pending', notes: ''},
    {name: 'Syarul', submissionDate: '19/2/2022',  score: 30,  client: 'JFV', recruiter: 'Sundqvist', status: 'Pending', notes: ''},
    {name: 'Zaffi', submissionDate: '19/2/2022',  score: 45,  client: 'JGV', recruiter: 'Patrik', status: 'Pending', notes: ''},
    {name: 'Jasper', submissionDate: '19/2/2022', score: 100,  client: 'JHV', recruiter: 'Sjölin', status: 'Pending', notes: ''},
    ]
    };
  },
  computed: {
  filteredcandidates() {
    let tempcandidates = this.candidates
    
    // Process search input
    if (this.searchValue != '' && this.searchValue) {
        tempcandidates = tempcandidates.filter((item) => {
          return item.name
            .toUpperCase()
            .includes(this.searchValue.toUpperCase())
        })
      }

        if (this.selectedRecruiter != 'All') {
          tempcandidates = tempcandidates.filter((item) => {
            return item.recruiter
              .toUpperCase()
              .includes(this.selectedRecruiter.toUpperCase())
          })
        }
        
        if (this.selectedClient != 'All') {
          tempcandidates = tempcandidates.filter((item) => {
            return item.client
              .toUpperCase()
              .includes(this.selectedClient.toUpperCase())
          })
        }

        if (this.selectedStatus != 'All') {
          tempcandidates = tempcandidates.filter((item) => {
            return item.status
              .toUpperCase()
              .includes(this.selectedStatus.toUpperCase())
          })
        }
      
      // Filter out by score
      if (this.minScore)
      tempcandidates = tempcandidates.filter((item) => {
        return ( item.score >= this.minScore && item.score <= this.maxScore)
      })

      //Filter by date
           
        
        return tempcandidates
      }
    },
    methods: {
      showFilter() {
        this.$refs['filter-menu'].toggle('#toggle-btn')
      }

    }
  }
  
</script>

<style>
.btn:focus {
  outline: none;
  box-shadow: none;
}
</style>