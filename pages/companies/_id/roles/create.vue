<template>
  <div>
    <section class="py-[70px] flex flex-col items-center justify-center px-4">
      <div class="text-[32px] font-semibold text-dark">New Role</div>
      <p class="mt-4 text-base leading-7 text-center mb-[50px] text-grey">
        Manage your employees to achieve <br />
        bigger goals for your company
      </p>
      <form class="w-full card"
            @submit.prevent="createRole">
        <div class="form-group">
          <label for=""
                 class="text-grey">Role Name</label>
          <input type="text"
                 class="input-field"
                 v-model="role.name" />
        </div>
        <ul id="listResp"
            class="flex flex-col gap-4">
          <li v-for="(resp, index) in responsibility"
              :key="index"
              class="inline-flex items-center w-full gap-5">
            <input type="text"
                   id="idRes"
                   name="responsibility"
                   v-model="resp.name"
                   class="w-full input-field" />
            <a href="#"
               role="button"
               @click="removeResponsibility(index)">
              <img src="/assets/svgs/ric-minus.svg"
                   alt="" />
            </a>
          </li>
          <li>
            <a href="#"
               role="button"
               @click="addResponsibility">
              <img src="/assets/svgs/ric-plus.svg"
                   alt="" />
            </a>
          </li>
        </ul>

        <button type="submit"
                class="w-full btn btn-primary mt-[14px]">
          Save Role
        </button>
      </form>
    </section>
  </div>
</template>

<script>
export default {
  layout: 'form',
  middleware: 'auth',
  data() {
    return {
      role: {
        name: '',
        company_id: this.$route.params.id,
      },
      responsibility: [
        { name: '' },
      ],
    };
  },
  methods: {
    addResponsibility() {
      this.responsibility.push({ name: '' });
    },
    removeResponsibility(index) {
      this.responsibility.splice(index, 1);
    },
    async createRole() {
      try {
        let response = await this.$axios.post('/role', this.role);
        let roleId = response?.data.result?.id;

        // Tambahkan role_id ke setiap responsibility
        this.responsibility.forEach((resp) => {
          resp.role_id = roleId;
        });

        // Convert responsibility data to JSON
        let responsibilityJson = JSON.stringify(this.responsibility);


        console.log(responsibilityJson)
        // Kirim data responsibility ke server
        await this.$axios.post('/responsibility', { responsibilities: responsibilityJson });

        // Redirect ke halaman roles setelah berhasil
        this.$router.push({ name: 'companies-id-roles' });
      } catch (error) {
        console.log(error);
      }
    },
  },
};
</script>
