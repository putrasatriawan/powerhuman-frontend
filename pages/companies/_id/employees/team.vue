<template>
  <div>
    <section class="py-[70px] flex flex-col items-center justify-center px-4">
      <div class="text-[32px] font-semibold text-dark">Add to Team</div>
      <p class="mt-4 text-base leading-7 text-center mb-[50px] text-grey">
        Add your new people to grow the <br />
        company reaching their goals
      </p>
      <div class="w-full card !max-w-[560px] sm:!flex-row items-center justify-between">
        <div class="flex flex-row items-center gap-4">
          <img src="/assets/images/user-f-1.png"
               width="70"
               alt="" />
          <div>
            <div class="text-lg font-semibold">{{ this.$store.state.employee.name }}</div>
            <p class="text-base text-grey">{{ this.$store.state.employee.email }}</p>
          </div>
        </div>
        <p class="text-right text-grey">{{ this.$store.state.employee.role_name }}</p>
      </div>

      <!-- Your Teams -->
      <section class="pt-[50px]">
        <!-- Section Header -->
        <div class="mb-[30px]">
          <div class="flex items-center justify-between gap-6">
            <div>
              <div class="text-xl font-medium text-dark">Your Teams</div>
              <p class="text-grey">Improve your growth</p>
            </div>
          </div>
        </div>

        <form>
          <p v-if="$fetchState.pending">Fetching teams...</p>
          <div class="grid grid-cols-2 gap-4 sm:grid-cols-3 lg:grid-cols-4 xl:gap-10 lg:gap-3 mb-[50px]"
               v-else>
            <div class="items-center card py-6 md:!py-10 md:!px-[38px] !gap-y-0"
                 v-for="team in  teams.data.result.data ">
              <input type="radio"
                     :value="team.id"
                     @click="updateTeamID"
                     name="team_id"
                     id="productGrowth"
                     class="absolute inset-0 checked:ring-2 ring-primary rounded-[26px] appearance-none" />
              <img :src="team.icon"
                   class="w-24"
                   alt="" />
              <div class="mt-6 mb-1 font-semibold text-center text-dark">
                {{ team.name }}
              </div>
              <p class="text-center text-grey">{{ team.employee_count }} People</p>
            </div>
          </div>
          <div class="flex justify-center">
            <button @click="createEmployee()"
                    type="button"
                    id="continueBtn"
                    class="btn btn-primary">
              Continue
            </button>
          </div>
        </form>
      </section>
    </section>
  </div>
</template>
<script>
export default {
  layout: 'form',
  middleware: 'auth',
  data() {
    return {
      teams: [],
    }
  },
  computed: {
    team_id() {
      return this.$store.state.employee.team_id
    },
  },
  async fetch() {
    this.teams = await this.$axios.get('/team', {
      params: {
        company_id: this.$route.params.id,
        limit: 100,
      },
    })
  },
  methods: {
    updateTeamID(event) {
      this.$store.commit('employee/updateTeamId', event.target.value)
    },
    async createEmployee() {
      try {
        const formData = new FormData();
        const state = this.$store.state.employee;

        formData.append('name', state.name);
        formData.append('photo', state.photo);
        formData.append('email', state.email);
        formData.append('gender', state.gender);
        formData.append('age', state.age);
        formData.append('phone', state.phone);
        formData.append('team_id', state.team_id);
        formData.append('role_id', state.role_id);

        let response = await this.$axios.post('/employee', formData, {
          headers: {
            'Content-Type': 'multipart/form-data',
          },
        });
        // console.log(response)
        //menghapus state karena udah di submit
        this.$store.commit('employee/updateName', '')
        this.$store.commit('employee/updatePhoto', '')
        this.$store.commit('employee/updateEmail', '')
        this.$store.commit('employee/updateGender', '')
        this.$store.commit('employee/updateAge', '')
        this.$store.commit('employee/updatePhone', '')
        this.$store.commit('employee/updateRoleId', '')
        this.$store.commit('employee/updateRoleName', '')
        this.$store.commit('employee/updateTeamId', '')

        //redirect ke employe page
        this.$router.push({
          name: 'companies-id-employees',
        })

        console.log(response)
      } catch (error) {
        console.log(error)
      }
    }
  }
}
</script>
