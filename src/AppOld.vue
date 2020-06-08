<template>
  <div style="width:80%">
    <table id="customers">
      <thead>
        <tr>
          <th colspan="2">Pieczęć</th>
          <th colspan="3">Odbiorca</th>
          <th><b>WZ</b></th>
          <td class="parent">
            <table class="small">
              <tr>
                <th>Nr bieżący</th>
                <th>Egz</th>
              </tr>
              <tr>
                <td><input type="text"></td>
                <td><input type="text"></td>
              </tr>
            </table>
          </td>
        </tr>
        <tr>
          <td colspan="2">Pieczęć</td>
          <td colspan="3"><input type="text"></td>
          <th>Wydanie materiałów / towarów na zewnątrz</th>
          <td class="parent">
            <table class="small">
              <tr>
                <th>Nr magazynowy</th>
                <th>Data wystawienia</th>
              </tr>
              <tr>
                <td><input type="text"></td>
                <td><input type="text"></td>
              </tr>
            </table>
          </td>
        </tr>
        <tr>
          <th>Środek transportu</th>
          <th>Zamówienie</th>
          <th>Przeznaczenie</th>
          <th>Data wysyłki</th>
          <th>Wysyłka na koszt</th>
          <th colspan="2">Nr i data faktury/ specyfikacji</th>
        </tr>
        <tr>
          <td><input type="text"></td>
          <td><input type="text"></td>
          <td><input type="text"></td>
          <td><input type="text"></td>
          <td><input type="text"></td>
          <td colspan="2"><input type="text"></td>
        </tr>
        <tr>
          <th>Kod towaru/ materiału</th>
          <th>Nazwa materiału/ towaru</th>
          <td class="parent">
            <table class="small">
              <tr>
                <th colspan="3">Ilość</th>
                
              </tr>
              <tr>
                <th>Zadysponowana</th>
                <th>J.m.</th>
                <th>Wydana</th>
              </tr>
            </table>
          </td>
          <th>Cena</th>
          <th>Wartość</th>
          <th>Konto synt.</th>
          <th>Zapas ilość</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(el,index) of rows" @click="saveRow(index)" :class="{activeRow:index==row}">
          <td><input type="text" v-model="el.kod"></td>
          <td><input type="text" v-model="el.produkt"></td>
          <td class="parent">
            <table style="">
              <td><input type="text" v-model="el.iloszZ"></td>
              <td><input type="text" v-model="el.miara"></td>
              <td><input type="text" v-model="el.iloscW"></td>
            </table>
          </td>
          <td><input type="text" v-model="el.cena"></td>
          <td><input type="text" v-model="el.wartosc"></td>
          <td><input type="text" v-model="el.kontoS"></td>
          <td><input type="text" v-model="el.zapasIlosc"></td>
        </tr>
        <tr>
          <td colspan="4" style="text-align:right">Razem</td>
          <td></td>
          <td colspan="2"></td>
        </tr>
      </tbody>
      <tfoot>
        <tr>
          <th>Wystawił</th>
          <th>Zatwierdził</th>
          <th>Wydał</th>
          <th>Data</th>
          <th>Odebrał</th>
          <th colspan="2">Ewidencja ilościowo-wartościowa</th>
        </tr>
        <tr>
          <td><input type="text"></td>
          <td><input type="text"></td>
          <td><input type="text"></td>
          <td><input type="text"></td>
          <td><input type="text"></td>
          <td colspan="2"><input type="text"></td>
        </tr>
      </tfoot>
    </table>
    <button @click="addItem">Add item</button>
    <button @click="deleteItem">Delete item</button>
    <button @click="shot">Print PDF</button>
  </div>
</template>

<script>
import html2canvas from 'html2canvas';
import jsPDF from 'jspdf';
export default {
  name: 'app',
  data () {
    return {
      rows:[],
      row:null,
      activeRow:false
    }
  },
  methods:{
    
    addItem(){
      this.rows.push({})
    },
    saveRow(i){
      this.row = i
      this.activeRow = true
    },
    deleteItem(){
      this.rows.splice(this.row,1)
    },
    shot(){
      html2canvas(document.querySelector('#customers'), {scale:0.5,quality:2}).then(canvas=>{
        document.body.appendChild(canvas)
      let pdf = new jsPDF('p','pt','a4')
      pdf.addImage(canvas.toDataURL('image/png'), 'PNG', 0, 0, 600, 700);
      pdf.save('WZ.pdf');})
    },
    print() {
		const filename  = 'WZ.pdf';

		html2canvas(document.querySelector('#customers'),{scale:1, quality:4}).then(canvas => {
			let pdf = new jsPDF('p', 'pt', 'a4');
			pdf.addImage(canvas.toDataURL('image/png'), 'PNG', 0, 0, 211, 298);
			pdf.save(filename);
		});
  },


  }
}
</script>

<style>

table {
  font-family: "Trebuchet MS", Arial, Helvetica, sans-serif;
  border-collapse: collapse;
  width: 100%;
  border: red solid 2px;
  font-size: 0.7em;
}

 td, th {
  border: 1px solid rgb(190, 101, 101);
  padding: 3px;
}


#customers th {

  text-align: center;
  background-color: rgb(52, 51, 61);
  
  color: white;
}
.parent{
  padding: 0;
}
.small{
  height: 100%;
}
tfoot td{
  padding: 20px 3px;
}
input{
  width: 90%;
  border: none;
  background: none;
}
input:focus{
  outline: none;
}
.activeRow{
  background-color: rgb(148, 148, 134);
}
</style>
