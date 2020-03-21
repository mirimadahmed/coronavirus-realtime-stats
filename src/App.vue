<template>
  <div>
    <h2 class="text-center m-2">CORONAVIRUS REALTIME STATS WORLDWIDE - UPDATED {{time}}</h2>
    <marquee-text :repeat="10" style="font-weight: bold;">{{ actualNews }}</marquee-text>
    <div class="row text-center m-auto p-2" v-if="countryData">
      <vue-world-map class="col-md-6" :countryData="countryData" />
      <div class="col-md-4 row p-0">
        <div class="col-12 p-1 m-0" v-for="total in totals" :key="total.text">
          <h2 class="text-left m-0" :class="total.class">{{total.text}}: {{total.count}}</h2>
        </div>
        <div class="col text-center p-2 rounded" v-for="(country, i) in top3" :key="i">
          <div class="lead">{{country.cases}}</div>
          <div>
            <country-flag :country="country.flag" size="big" />
          </div>
          <div class>D: {{country.deaths}}</div>
          <div class>R: {{country.total_recovered}}</div>
          <div class>A: {{country.active_cases}}</div>
        </div>
      </div>
      <div class="col-md-2 row m-0" v-if="data">
        <div class="col text-center p-2" v-for="(country, i) in rest10" :key="i">
          <div>{{country.cases}}</div>
          <div>
            <country-flag :country="country.flag" size="medium" />
          </div>
        </div>
      </div>
    </div>
    <div class="row p-2" v-if="data">
      <div class="col text-center m-1 p-1" v-for="(country, i) in rest" :key="i">
        <div>{{country.cases}}</div>
        <div>
          <country-flag :country="country.flag" size="small" />
        </div>
      </div>
    </div>
    <audio controls id="myVideo" autoplay loop>
      <source src="https://bookable.pk/audio.wav" type="audio/wav" />Your browser does not support the audio element.
    </audio>
  </div>
</template>

<script>
import * as firebase from "firebase/app";
import "firebase/database";

import VueWorldMap from "vue-world-map";
import MarqueeText from "vue-marquee-text-component";
import CountryFlag from "vue-country-flag";
import moment from "moment";
var firebaseConfig = {
  apiKey: "XXXXXXXXXXXXXXXXXXXXXXXXX",
  authDomain: "XXXXXXXXXXXXXXXXXXXXXXXXX",
  databaseURL: "XXXXXXXXXXXXXXXXXXXXXXXXX",
  projectId: "XXXXXXXXXXXXXXXXXXXXXXXXX",
  storageBucket: "XXXXXXXXXXXXXXXXXXXXXXXXX",
  messagingSenderId: "XXXXXXXXXXXXXXXXXXXXXXXXX",
  appId: "XXXXXXXXXXXXXXXXXXXXXXXXX"
};
firebase.initializeApp(firebaseConfig);
let db = firebase.database();

export default {
  name: "App",
  components: {
    CountryFlag,
    VueWorldMap,
    MarqueeText
  },
  data() {
    return {
      news: [],
      intervalId: null,
      lastTime: "now",
      totals: [],
      data: null,
      isoCountries: {
        Afghanistan: "AF",
        "Aland Islands": "AX",
        Albania: "AL",
        Algeria: "DZ",
        "American Samoa": "AS",
        Andorra: "AD",
        Angola: "AO",
        Anguilla: "AI",
        Antarctica: "AQ",
        "Antigua And Barbuda": "AG",
        Argentina: "AR",
        Armenia: "AM",
        Aruba: "AW",
        Australia: "AU",
        Austria: "AT",
        Azerbaijan: "AZ",
        Bahamas: "BS",
        Bahrain: "BH",
        Bangladesh: "BD",
        Barbados: "BB",
        Belarus: "BY",
        Belgium: "BE",
        Belize: "BZ",
        Benin: "BJ",
        Bermuda: "BM",
        Bhutan: "BT",
        Bolivia: "BO",
        "Bosnia and Herzegovina": "BA",
        Botswana: "BW",
        "Bouvet Island": "BV",
        Brazil: "BR",
        "British Indian Ocean Territory": "IO",
        Brunei: "BN",
        Bulgaria: "BG",
        "Burkina Faso": "BF",
        Burundi: "BI",
        Cambodia: "KH",
        Cameroon: "CM",
        Canada: "CA",
        "Cape Verde": "CV",
        "Cayman Islands": "KY",
        "Central African Republic": "CF",
        Chad: "TD",
        Chile: "CL",
        China: "CN",
        "Christmas Island": "CX",
        "Cocos (Keeling) Islands": "CC",
        Colombia: "CO",
        Comoros: "KM",
        Congo: "CG",
        "Congo, Democratic Republic": "CD",
        "Cook Islands": "CK",
        "Costa Rica": "CR",
        "Cote D'Ivoire": "CI",
        Croatia: "HR",
        Cuba: "CU",
        Cyprus: "CY",
        Czechia: "CZ",
        Denmark: "DK",
        Djibouti: "DJ",
        Dominica: "DM",
        "Dominican Republic": "DO",
        Ecuador: "EC",
        Egypt: "EG",
        "El Salvador": "SV",
        "Equatorial Guinea": "GQ",
        Eritrea: "ER",
        Estonia: "EE",
        Ethiopia: "ET",
        "Falkland Islands": "FK",
        "Faeroe Islands": "FO",
        Fiji: "FJ",
        Finland: "FI",
        France: "FR",
        "French Guiana": "GF",
        "French Polynesia": "PF",
        "French Southern Territories": "TF",
        Gabon: "GA",
        Gambia: "GM",
        Georgia: "GE",
        Germany: "DE",
        Ghana: "GH",
        Gibraltar: "GI",
        Greece: "GR",
        Greenland: "GL",
        Grenada: "GD",
        Guadeloupe: "GP",
        Guam: "GU",
        Guatemala: "GT",
        Guernsey: "GG",
        Guinea: "GN",
        "Guinea-Bissau": "GW",
        Guyana: "GY",
        Haiti: "HT",
        "Heard Island & Mcdonald Islands": "HM",
        "Holy See (Vatican City State)": "VA",
        Honduras: "HN",
        "Hong Kong": "HK",
        Hungary: "HU",
        Iceland: "IS",
        India: "IN",
        Indonesia: "ID",
        Iran: "IR",
        Iraq: "IQ",
        Ireland: "IE",
        "Isle Of Man": "IM",
        Israel: "IL",
        Italy: "IT",
        Jamaica: "JM",
        Japan: "JP",
        Jersey: "JE",
        Jordan: "JO",
        Kazakhstan: "KZ",
        Kenya: "KE",
        Kiribati: "KI",
        "S. Korea": "KR",
        Kuwait: "KW",
        Kyrgyzstan: "KG",
        "Lao People's Democratic Republic": "LA",
        Latvia: "LV",
        Lebanon: "LB",
        Lesotho: "LS",
        Liberia: "LR",
        "Libyan Arab Jamahiriya": "LY",
        Liechtenstein: "LI",
        Lithuania: "LT",
        Luxembourg: "LU",
        Macao: "MO",
        "North Macedonia": "MK",
        Madagascar: "MG",
        Malawi: "MW",
        Malaysia: "MY",
        Maldives: "MV",
        Mali: "ML",
        Malta: "MT",
        "Marshall Islands": "MH",
        Martinique: "MQ",
        Mauritania: "MR",
        Mauritius: "MU",
        Mayotte: "YT",
        Mexico: "MX",
        "Micronesia, Federated States Of": "FM",
        Moldova: "MD",
        Monaco: "MC",
        Mongolia: "MN",
        Montenegro: "ME",
        Montserrat: "MS",
        Morocco: "MA",
        Mozambique: "MZ",
        Myanmar: "MM",
        Namibia: "NA",
        Nauru: "NR",
        Nepal: "NP",
        Netherlands: "NL",
        "Netherlands Antilles": "AN",
        "New Caledonia": "NC",
        "New Zealand": "NZ",
        Nicaragua: "NI",
        Niger: "NE",
        Nigeria: "NG",
        Niue: "NU",
        "Norfolk Island": "NF",
        "Northern Mariana Islands": "MP",
        Norway: "NO",
        Oman: "OM",
        Pakistan: "PK",
        Palau: "PW",
        Palestine: "PS",
        Panama: "PA",
        "Papua New Guinea": "PG",
        Paraguay: "PY",
        Peru: "PE",
        Philippines: "PH",
        Pitcairn: "PN",
        Poland: "PL",
        Portugal: "PT",
        "Puerto Rico": "PR",
        Qatar: "QA",
        Réunion: "RE",
        Romania: "RO",
        Russia: "RU",
        Rwanda: "RW",
        "Saint Barthelemy": "BL",
        "Saint Helena": "SH",
        "Saint Kitts And Nevis": "KN",
        "Saint Lucia": "LC",
        "Saint Martin": "MF",
        "Saint Pierre And Miquelon": "PM",
        "Saint Vincent And Grenadines": "VC",
        Samoa: "WS",
        "San Marino": "SM",
        "Sao Tome And Principe": "ST",
        "Saudi Arabia": "SA",
        Senegal: "SN",
        Serbia: "RS",
        Seychelles: "SC",
        "Sierra Leone": "SL",
        Singapore: "SG",
        Slovakia: "SK",
        Slovenia: "SI",
        "Solomon Islands": "SB",
        Somalia: "SO",
        "South Africa": "ZA",
        "South Georgia And Sandwich Isl.": "GS",
        Spain: "ES",
        "Sri Lanka": "LK",
        Sudan: "SD",
        Suriname: "SR",
        "Svalbard And Jan Mayen": "SJ",
        Swaziland: "SZ",
        Sweden: "SE",
        Switzerland: "CH",
        "Syrian Arab Republic": "SY",
        Taiwan: "TW",
        Tajikistan: "TJ",
        Tanzania: "TZ",
        Thailand: "TH",
        "Timor-Leste": "TL",
        Togo: "TG",
        Tokelau: "TK",
        Tonga: "TO",
        "Trinidad And Tobago": "TT",
        Tunisia: "TN",
        Turkey: "TR",
        Turkmenistan: "TM",
        "Turks And Caicos Islands": "TC",
        Tuvalu: "TV",
        Uganda: "UG",
        Ukraine: "UA",
        UAE: "AE",
        UK: "GB",
        USA: "US",
        "United States Outlying Islands": "UM",
        Uruguay: "UY",
        Uzbekistan: "UZ",
        Vanuatu: "VU",
        Venezuela: "VE",
        Vietnam: "VN",
        "Virgin Islands, British": "VG",
        "Virgin Islands, U.S.": "VI",
        "Wallis And Futuna": "WF",
        "Western Sahara": "EH",
        Yemen: "YE",
        Zambia: "ZM",
        Zimbabwe: "ZW"
      },
      countryData: null
    };
  },
  computed: {
    time() {
      return moment(this.lastTime).fromNow();
    },
    top3() {
      // eslint-disable-next-line
      return this.data ? this.data.slice(0, 5) : [];
    },
    rest10() {
      // eslint-disable-next-line
      return this.data ? this.data.slice(5, 20) : [];
    },
    rest() {
      // eslint-disable-next-line
      return this.data ? this.data.slice(20, this.data.length) : [];
    },
    actualNews() {
      return Object.values(this.news).join(", ") + " --> ";
    }
  },
  methods: {
    getTime(time) {
      return moment(time).fromNow();
    },
    getCountryCode(country) {
      // eslint-disable-next-line
      if (this.isoCountries.hasOwnProperty(country)) {
        return this.isoCountries[country];
      } else {
        return country;
      }
    },
    fetch() {
      fetch(
        "https://coronavirus-monitor.p.rapidapi.com/coronavirus/cases_by_country.php",
        {
          method: "GET",
          headers: {
            "x-rapidapi-host": "coronavirus-monitor.p.rapidapi.com",
            "x-rapidapi-key":
              "XXXXXXXXXXXXXXXXXXXXXXXXX"
          }
        }
      )
        .then(response =>
          response.json().then(data => {
            this.countryData = {};
            let total = 0,
              deaths = 0,
              recovered = 0,
              ongoing = 0;
            data.countries_stat.forEach(item => {
              item.flag = this.getCountryCode(item.country_name);
              item.cases = item.cases.split(",").join("");
              item.deaths = item.deaths.split(",").join("");
              item.total_recovered = item.total_recovered.split(",").join("");
              item.active_cases = item.active_cases.split(",").join("");
              this.countryData[item.flag] = parseInt(item.cases);
              total += parseInt(item.cases);
              deaths += parseInt(item.deaths);
              recovered += parseInt(item.total_recovered);
              ongoing += parseInt(item.active_cases);
            });
            this.totals = [];
            this.totals.push({
              class: "alert bg-primary",
              count: total,
              text: "Total"
            });
            this.totals.push({
              class: "alert bg-warning",
              count: ongoing,
              text: "Active"
            });
            this.totals.push({
              class: "alert bg-danger",
              count: deaths,
              text: "Deaths"
            });
            this.totals.push({
              class: "alert bg-success",
              count: recovered,
              text: "Recovered"
            });
            this.data = data.countries_stat.sort(function(a, b) {
              return b.cases - a.cases;
            });
            this.lastTime = new Date();
          })
        )
        .catch(err => {
          console.log(err);
        });
    }
  },
  mounted() {
    this.fetch();
    this.intervalId = setInterval(() => this.fetch(), 5000);
    db.ref("news").on("value", snapshot => {
      this.news = snapshot.val();
    });
  },
  beforeDestroy() {
    clearInterval(this.intervalId);
  }
};
</script>

<style>
* {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  background: #2c3e50;
  color: white;
}
.marequee-text-wrap {
  overflow: hidden;
}
.marequee-text-content {
  width: 100000px;
}
.marequee-text-text {
  animation-name: animation;
  animation-timing-function: linear;
  animation-iteration-count: infinite;
  float: left;
}
.marequee-text-paused .marequee-text-text {
  animation-play-state: paused;
}
@keyframes animation {
  0% {
    transform: translateX(0);
  }
  100% {
    transform: translateX(-100%);
  }
}
</style>
