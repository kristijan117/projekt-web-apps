<template>
<div>
    <section id="cart_items">
		<div class="container">
            <br><br>
			<div class="table-responsive cart_info">
				<table class="table table-condensed">
					<thead>
						<tr class="cart_menu">
							<td class="image"></td>
							<td class="price">Količina</td>
							<td class="quantity">Cijena</td>
							<!--<td class="total">Briši stanje košarice</td>-->
							<td></td>
						</tr>
					</thead>
					<tbody>
						<tr style="margin-top:20px">
							<td class="cart_description">
								<h4><a href="">Vaša košarica: </a></h4>
							</td>
							<td class="cart_quantity">
								<div class="cart_quantity_button">
									{{cartQty}}
								</div>
							</td>
							<td class="cart_total">
								<p class="cart_total_price">{{cartPrice}} kn</p>
							</td>
							<!--<td class="cart_delete">
								<a style="margin-left:50px" class="cart_quantity_delete" href=""><i class="fa fa-times"></i></a>
							</td>-->
						</tr>
					</tbody>
				</table>
			</div>
		</div>
	</section> <!--/#cart_items-->

	<section id="do_action">
		<div class="container">
			<div class="heading">
				<h3>Nastavite s kupnjom</h3>
			</div>
			<div class="row">
				<div class="col-sm-6">
					<div class="chose_area">
						
						<ul class="user_info">
							<li class="single_field">
								<label>Država:</label>
								<select v-model="drzava">
									<option>Hrvatska</option>
									<option>Bosna i Hercegovina</option>
									<option>Srbija</option>
									<option>Crna Gora</option>
									<option>Austrija</option>
									<option>Njemačka</option>
									<option>Poljska</option>
									<option>Ukrajina</option>
								</select>
								
							</li>
							<li class="single_field zip-field">
								<label>Grad:</label>
								<input v-model="grad" type="text">
							
							</li>
							<li class="single_field zip-field">
								<label>Poštanski broj:</label>
								<input v-model="postanski_broj" type="text">
							</li>
						</ul>
					</div>
				</div>
				<div class="col-sm-6">
					<div class="total_area">
						<ul>
							<li>Ukupna potrošnja iz košarice<span>{{cartPrice}} kn</span></li>
							<li>Porez <span>{{cartTax.toFixed(2)}} kn</span></li>
							<li>Dostava <span>Besplatno</span></li>
							<li>Ukupno<span>{{cartPrice}} kn</span></li>
						</ul>
							<a class="btn btn-default check_out" @click="confirmData()">Kupi</a>
							<p v-if="warnMessage" style="color:red">Ispunite sve podatke!</p>
					</div>
				</div>
			</div>
		</div>
        
	</section><!--/#do_action-->
    <Footer />
</div>
</template>

<script>
// @ is an alias to /src
import Footer from '@/components/footer.vue'
import { Cart, Auth, Payment } from '@/services'

export default {
  name: 'Home',
  components: {
	Footer
  },
  data(){
	return {
		auth: Auth.state,
		cartItems: [],
		cartPrice: 0,
		cartQty: 0,
		cartTax: 0,
		drzava: "",
		grad:"",
		postanski_broj:"",
		warnMessage:false
	}
  },
  methods:{
	async cartData(){
			let dohvat = await Cart.fetchCart();
			this.cartItems = JSON.parse(JSON.stringify(dohvat.cart));
			this.cartPrice = this.cartItems.ukupnaCijena.toFixed(2);
			this.cartQty = this.cartItems.ukupnaKol;
			this.cartTax = this.cartPrice*0.05;
		},
	async confirmData(){
		if(this.cartPrice !=="" && this.cartQty !=="" && this.drzava !=="" && this.grad !=="" && this.postanski_broj !=="" && this.auth.userEmail !==""){
		let placanje = {
                cijena: this.cartPrice,
                kolicinaProizvoda:this.cartQty,
				drzava: this.drzava,
				grad: this.grad,
				postanski_broj: this.postanski_broj,
				korisnik: this.auth.userEmail
            }
		Payment.pay(placanje);
		}
		else{
			this.warnMessage=true;
		}
	}
  },
  created(){
		this.cartData()
  }
}
</script>

