# LTE-FDD Capacity Calculator

If you are interested in the theory of how the calculations are performed, then jump to [LTE FDD Capacity Calculation](ltefdddimensioning.md)

The calculator will try to provide Cell Capacity based on the Control channel overheads, RF Condition parameters and Cell Configuration parameters supplied.

Please note that all parameters like Usage or Ratio are to be provided between 0 to 1 where 1 means 100%, 0.5 means 50% and so on.

I would appreciate any feedback or questions aliasgherman at gmail



<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/materialize/1.0.0/css/materialize.min.css">
<script src="https://cdnjs.cloudflare.com/ajax/libs/materialize/1.0.0/js/materialize.min.js"></script>



<div class="row">
    <div class="input-field col s12 m6 l6">
          <input id="cfi1_usage" type="number" class="validate" min="0.0" max="1.0" step="0.01" value="0.6">
          <label for="cfi1_usage">CFI 1 Usage (0.0 - 1.0)</label>
    </div>
</div>

<div class="row">
    <div class="input-field col s12 m6 l6">
          <input id="cfi2_usage" type="number" class="validate" min="0.0" max="1.0" step="0.01" value="0.3">
          <label for="cfi2_usage">CFI 2 Usage (0.0 - 1.0)</label>
    </div>
</div>

<div class="row">
    <div class="input-field col s12 m6 l6">
          <input id="cfi3_usage" type="number" class="validate" min="0.0" max="1.0" step="0.01" value="0.1">
          <label for="cfi3_usage">CFI 3 Usage (0.0 - 1.0)</label>
    </div>
</div>

<div class="row">
    <div class="input-field col s12 m6 l6">
    <select id="bandwidth_select">
      <option value="1.4">1.4</option>
      <option value="3">3</option>
      <option value="5">5</option>
      <option value="10">10</option>
      <option value="15">15</option>
      <option value="20" selected>20</option>        
    </select>
    <label>Select Bandwidth (MHz)</label>
    </div>
</div>	

<div class="row">
    <div class="input-field col s12 m6 l6" >
    <select id="antenna_select">
      <option value="1">1</option>
      <option value="2">2</option>
      <option value="4" selected>4</option>
    </select>
    <label>Select Antenna Ports</label>
    </div>
</div>

<div class="row">
    <div class="input-field col s12 m6 l6">
          <input id="ibler_ratio" type="number" class="validate" min="0.0" max="1.0" step="0.01" value="0.12">
          <label for="ibler_ratio">IBLER Ratio</label>
    </div>
</div>	
<div class="row">
    <div class="input-field col s12 m6 l6">
          <input id="cqi_value" type="number" class="validate" min="0.0" max="15.0" step="0.01" value="10.3">
          <label for="cqi_value">CQI (0 - 15)</label>
    </div>
</div>
<div class="row">
    <div class="input-field col s12 m6 l6">
          <input id="qam256_ratio" type="number" class="validate" min="0.0" max="1.0" step="0.01" value="0.09">
          <label for="qam256_ratio">256QAM Ratio</label>
    </div>
</div>	
<div class="row">
    <div class="input-field col s12 m6 l6">
          <input id="rank2_ratio" type="number" class="validate" min="0.0" max="1.0" step="0.01" value="0.60">
          <label for="rank2_ratio">Rank2 Usage</label>
    </div>
</div>

<div class="row">	
    <div class="input-field col s12 m6 l6">
          <input id="rank3_ratio" type="number" class="validate" min="0.0" max="1.0" step="0.01" value="0.09">
          <label for="rank3_ratio">Rank3 Usage</label>
    </div>	
</div>

<div class="row">	
    <div class="input-field col s12 m6 l6">
          <input id="rank4_ratio" type="number" class="validate" min="0.0" max="1.0" step="0.01" value="0.21">
          <label for="rank4_ratio">Rank4 Usage</label>
    </div>
</div>

<div class="row">
    <div class="input-field col s12 m6 l6">
        <a class="waves-effect waves-light btn" id="calculate_control_overheads" onclick="calc_overheads()">Calculate Overheads</a>
    </div>
</div>

<div class="row">
	<div id="calculation_output">
		<h1>Output will appear here</h1>
	</div>
</div>

<script>

window.onload = function() {
  M.AutoInit();
  console.log("Page loaded");
}

function calc_overheads() {
	console.log("Inside the function");
	var cfi1 = document.getElementById("cfi1_usage").valueAsNumber;
	var cfi2 = document.getElementById("cfi2_usage").valueAsNumber;
	var cfi3 = document.getElementById("cfi3_usage").valueAsNumber;
	var cqi = document.getElementById("cqi_value").valueAsNumber;
	var rank2 = document.getElementById("rank2_ratio").valueAsNumber;
	var rank3 = document.getElementById("rank3_ratio").valueAsNumber;
	var rank4 = document.getElementById("rank4_ratio").valueAsNumber;
	var ibler = document.getElementById("ibler_ratio").valueAsNumber;
	var qam256 = document.getElementById("qam256_ratio").valueAsNumber;
	
	console.log(cfi1+cfi2+cfi3);
	
	var numantenna = document.getElementById("antenna_select").value;
	var bandwidth = document.getElementById("bandwidth_select").value;
	
	console.log(numantenna);
	console.log(bandwidth);
	
	if (cfi1 < 0 || cfi1 > 1) {
		M.toast({html: "<div><B>ERROR</B><br /> CFI1 Usage should be a number between 0 to 1</div>", classes: 'rounded'});
		return;
	}
	if (cfi2 < 0 || cfi2 > 1) {
		M.toast({html: "<div><B>ERROR</B><br /> CFI2 Usage should be a number between 0 to 1</div>", classes: 'rounded'});
		return;
	}
	if (cfi3 < 0 || cfi3 > 1) {
		M.toast({html: "<div><B>ERROR</B><br /> CFI3 Usage should be a number between 0 to 1</div>", classes: 'rounded'});
		return;
	}
	if ( (cfi1 + cfi2 + cfi3).toFixed(2) != 1.0) {
		M.toast({html: "<div><B>ERROR</B><br /> CFI1 + CFI2 + CFI3 usage should add upto 1</div>", classes: 'rounded'});
		return;
	}
	
	if ((numantenna != 1) && (numantenna != 2) && (numantenna != 4)) {
		M.toast({html: "<div><B>ERROR</B><br /> Num antennas should be 1,2,4</div>", classes: 'rounded'});
		return;	
	}
	
	if (bandwidth != 1.4 && bandwidth != 3 && bandwidth != 5 && bandwidth != 10 && bandwidth != 10 && bandwidth != 15 && bandwidth != 20) {
		M.toast({html: "<div><B>ERROR</B><br /> Bandwidth should be 1.4, 3, 5, 10, 15 or 20MHz</div>", classes: 'rounded'});
		return;	
	}	
	if ((bandwidth < 10) && (cfi1 > 0)) {
		M.toast({html: "<div><B>ERROR</B><br /> For bandwidths 1.4, 3 & 5, please keep CFI1 Usage=0 and use only CFI2+CFI3 values such that CFI2+CFI3=1.0</div>", classes: 'rounded'});
		return;		
	}
	
	if (( (rank2) > 0 ) && ( numantenna == 1)) {
		M.toast({html: "<div><B>ERROR</B><br /> If Antenna is 1, then Rank2,3,4 should be zero.</div>", classes: 'rounded'});
		return;
	}
	
	if (( (rank3 + rank4) > 0 ) && ( numantenna < 4)) {
		M.toast({html: "<div><B>ERROR</B><br /> If Antenna < 4, then Rank 3,4 should be zero.</div>", classes: 'rounded'});
		return;
	}
	
	if (( (rank2 + rank3 + rank4).toFixed(2) > 1.0 )) {
		M.toast({html: "<div><B>ERROR</B><br /> The sum of Rank2, 3, 4 should be <= 1.0 as they are ratio of all ranks and remaining amount will be rank1 usage.</div>", classes: 'rounded'});
		return;
	}
	
	if ( cqi < 0 || cqi > 15) {
		M.toast({html: "<div><B>ERROR</B><br /> CQI should be between 0 to 15.</div>", classes: 'rounded'});
		return;
	}
	
	if (ibler < 0 || ibler > 1.0) {
		M.toast({html: "<div><B>ERROR</B><br /> IBLER should be between 0 to 1.0.</div>", classes: 'rounded'});
		return;
	}
	
	
	var rb_in_slot = 6;
	if (bandwidth == 1.4) rb_in_slot = 6;
	if (bandwidth == 3) rb_in_slot = 15;
	if (bandwidth == 5) rb_in_slot = 25;
	if (bandwidth == 10) rb_in_slot = 50;
	if (bandwidth == 15) rb_in_slot = 75;
	if (bandwidth == 20) rb_in_slot = 100;
	
	var re_in_frame_exc_mimo = rb_in_slot * 12 * 10 * 14;
	var avg_cfi = cfi1 * 1 + cfi2 * 2 + cfi3 * 3;
	
	var crs_overheads = 0;
	if (bandwidth == 1.4) crs_overheads = 480;
	if (bandwidth == 3) crs_overheads = 1200;
	if (bandwidth == 5) crs_overheads = 2000;
	if (bandwidth == 10) crs_overheads = 4000;
	if (bandwidth == 15) crs_overheads = 6000;
	if (bandwidth == 20) crs_overheads = 8000;
	crs_overheads = crs_overheads * numantenna;
	
	var pss_sss_overhead = 288;
	var pbch_overhead = 240;
	
	var pcfich_overhead = 160;
	if (numantenna == 1) pcfich_overhead = 200; //additional overheads as PBCH considers interleaving like 2/4 port antennas
	
	var phich_overhead = 120;
	if (bandwidth == 1.4) phich_overhead = 120;	
	if (bandwidth == 3) phich_overhead = 240;	
	if (bandwidth == 5) phich_overhead = 480;	
	if (bandwidth == 10) phich_overhead = 840;	
	if (bandwidth == 15) phich_overhead = 1200;	
	if (bandwidth == 20) phich_overhead = 1560;	

	var pdcch_overhead_cfi1 = 0;
	if (bandwidth == 1.4) pdcch_overhead_cfi1 = 0;
	if (bandwidth == 3) pdcch_overhead_cfi1 = 0;
	if (bandwidth == 5) pdcch_overhead_cfi1 = 0;
	if (bandwidth == 10) pdcch_overhead_cfi1 = 3000;
	if (bandwidth == 15) pdcch_overhead_cfi1 = 4640;
	if (bandwidth == 20) pdcch_overhead_cfi1 = 6280;
	
	var pdcch_overhead_cfi2 = 0;
	if (bandwidth == 1.4) pdcch_overhead_cfi2 = 1640;
	if (bandwidth == 3) pdcch_overhead_cfi2 = 2600;
	if (bandwidth == 5) pdcch_overhead_cfi2 = 4360;
	if (bandwidth == 10) pdcch_overhead_cfi2 = 9000;
	if (bandwidth == 15) pdcch_overhead_cfi2 = 13640;
	if (bandwidth == 20) pdcch_overhead_cfi2 = 18280;
	
	var pdcch_overhead_cfi3 = 0;
	if (bandwidth == 1.4) pdcch_overhead_cfi3 = 2360;
	if (bandwidth == 3) pdcch_overhead_cfi3 = 4400;
	if (bandwidth == 5) pdcch_overhead_cfi3 = 7360;
	if (bandwidth == 10) pdcch_overhead_cfi3 = 15000;
	if (bandwidth == 15) pdcch_overhead_cfi3 = 22640;
	if (bandwidth == 20) pdcch_overhead_cfi3 = 30280;	
	
	var pdcch_overhead = pdcch_overhead_cfi1 * cfi1 + pdcch_overhead_cfi2 * cfi2 + pdcch_overhead_cfi3 * cfi3;
	
	var pdsch_remaining_wo_mimo = re_in_frame_exc_mimo - (crs_overheads + pdcch_overhead + pss_sss_overhead + pbch_overhead + pcfich_overhead + phich_overhead);
	var pdsch_remaining_with_mimo = pdsch_remaining_wo_mimo * numantenna;
	
	
	var cqi_table_1 = [0, 0.1523, 0.2344, 0.377, 0.6016, 0.877, 1.1758, 1.4766, 1.9141, 2.4063, 2.7305, 3.3223, 3.9023, 4.5234, 5.1152, 5.5547];
	var cqi_table_2 = [0, 0.1523, 0.377, 0.877, 1.4766, 1.9141, 2.4063, 2.7305, 3.3223, 3.9023, 4.5234, 5.1152, 5.5547, 6.2266, 6.9141, 7.4063];
	
	
	var eff1 = cqi_table_1[Math.floor(cqi)];
	var eff2 = cqi_table_1[Math.ceil(cqi)];
	var cqi_diff = cqi - Math.floor(cqi);
	
	var eff_table1 = (eff2 - eff1) * (cqi_diff) + eff1;
	
	eff1 = cqi_table_2[Math.floor(cqi)];
	eff2 = cqi_table_2[Math.ceil(cqi)];
	cqi_diff = cqi - Math.floor(cqi);
	
	var eff_table2 = (eff2 - eff1) * (cqi_diff) + eff1;
	
	var efficiency = eff_table1 * (1 - qam256) + eff_table2 * (qam256);
	
	console.log(pdsch_remaining_wo_mimo);
	console.log(efficiency);

	console.log(rank_efficiency);
	console.log((1 - ibler));
	
	var rank_efficiency = 0;
	if (rank2 > 0) {
		rank_efficiency += (2 * rank2);
	}
	if (rank3 > 0) {
		rank_efficiency += (3 * rank3);
	}
	if (rank4 > 0) {
		rank_efficiency += (4 * rank4);
	}	
	
	var cell_capacity = (100 * pdsch_remaining_wo_mimo) * efficiency * rank_efficiency * (1 - ibler) / 1024 / 1024;
	cell_capacity = cell_capacity.toFixed(2);
	
	console.log("Cell capacity : " + cell_capacity);
	

	var output_toast = "<table class='centered'> ";
	output_toast += '<tr><th>Cell Capacity (Mbps)</th><td>' + cell_capacity + '</td></tr>' ;
	output_toast += '<tr><th>Total RE in Frame (Single Antenna Port)</th><td>' + re_in_frame_exc_mimo + '</td></tr>' ;
	output_toast += '<tr><th>Remaining PDSCH RE in Frame (Single Antenna Port)</th><td>' + pdsch_remaining_wo_mimo + '</td></tr>' ;
	output_toast += '<tr><th>Control Overhead in Frame (All Antenna Port Overheads)</th><td>' + (re_in_frame_exc_mimo - pdsch_remaining_wo_mimo) + '</td></tr>' ;
	output_toast += '</table>';
	output_toast = "<div class='blue lighten-4'> " + output_toast + " </div>"; 
	
	var output_table = "<table class='centered'> ";
	output_table += '<tr><th>Cell Capacity (Mbps)</th><td>' + cell_capacity + '</td></tr>' ;
	output_table += '<tr><th>Efficiency due to Higher Rank</th><td>' + rank_efficiency + '</td></tr>' ;
	output_table += '<tr><th>CQI Efficiency</th><td>' + efficiency + '</td></tr>' ;
	output_table += '<tr><th>IBLER</th><td>' + ibler + '</td></tr>' ;
	
	output_table += '<tr><th>Remaining PDSCH RE in Frame (w/o MIMO)</th><td>' + pdsch_remaining_wo_mimo + '</td></tr>' ;
	output_table += '<tr><th>Remaining PDSCH RE in Frame (with MIMO)</th><td>' + pdsch_remaining_with_mimo + '</td></tr>' ;	
	output_table += '<tr><th>Bandwidth (Mhz)</th><td>' + bandwidth + '</td></tr>' ;
	output_table += '<tr><th>Antenna Ports</th><td>' + numantenna + '</td></tr>' ;
	output_table += '<tr><th>RBs</th><td>' + rb_in_slot + '</td></tr>' ;
	output_table += '<tr><th>Total RE in a Frame (w/o MIMO)</th><td>' + re_in_frame_exc_mimo + '</td></tr>' ;
	output_table += '<tr><th>CFI1 Ratio (0-1)</th><td>' + cfi1 + '</td></tr>' ;
	output_table += '<tr><th>CFI2 Ratio (0 - 1)</th><td>' + cfi2 + '</td></tr>' ;
	output_table += '<tr><th>CFI3 Ratio (0-1)</th><td>' + cfi3 + '</td></tr>' ;
	output_table += '<tr><th>Average CFI</th><td>' + avg_cfi + '</td></tr>' ;
	output_table += '<tr><th>CRS Overheads</th><td>' + crs_overheads + '</td></tr>' ;
	output_table += '<tr><th>PDCCH Overheads</th><td>' + pdcch_overhead + '</td></tr>' ;
	output_table += '<tr><th>PSS/SSS Overheads</th><td>' + pss_sss_overhead + '</td></tr>' ;
	output_table += '<tr><th>PBCH Overheads</th><td>' + pbch_overhead + '</td></tr>' ;
	output_table += '<tr><th>PCFICH Overheads</th><td>' + pcfich_overhead + '</td></tr>' ;
	output_table += '<tr><th>PHICH Overheads</th><td>' + phich_overhead + '</td></tr>' ;
	output_table += "</table>";

	var output_message = "<div class='blue lighten-4'> " + output_table + " </div>"; 
	


	
	document.getElementById("calculation_output").innerHTML = output_message;
    M.toast({html: output_toast, classes: 'rounded', displayLength: 10000});
}
  
</script>
