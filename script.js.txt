let chart; // global variable for the chart

// Logistic growth calculation
function logisticGrowth(V0, k, M, t) {
    let V = M / (1 + ((M - V0)/V0) * Math.exp(-k*t));
    let dVdt = k * V * (1 - V/M);
    return { V, dVdt };
}

// Time to reach target volume
function timeToReach(V0, k, M, targetV) {
    if (targetV >= M) return Infinity;
    return -Math.log((M/targetV - 1)/(M/V0 - 1)) / k;
}

// Draw the tumor growth chart
function drawChart(V0, k, M) {
    let labels = [];
    let data = [];
    for (let t = 0; t <= 30; t++) {
        labels.push(t);
        let V = M / (1 + ((M - V0)/V0) * Math.exp(-k*t));
        data.push(V.toFixed(2));
    }

    const ctx = document.getElementById('tumorChart').getContext('2d');
    
    if (chart) chart.destroy(); // remove previous chart if exists
    
    chart = new Chart(ctx, {
        type: 'line',
        data: {
            labels: labels,
            datasets: [{
                label: 'Tumor Volume (cm³)',
                data: data,
                borderColor: 'red',
                backgroundColor: 'rgba(255,0,0,0.2)',
                fill: true,
            }]
        },
        options: {
            responsive: true,
            plugins: {
                legend: { display: true },
                title: { display: true, text: 'Logistic Tumor Growth Curve' }
            },
            scales: {
                x: { title: { display: true, text: 'Time (days)' } },
                y: { title: { display: true, text: 'Tumor Volume (cm³)' } }
            }
        }
    });
}

// Calculate results and update chart
function calculate() {
    let V0 = parseFloat(document.getElementById('V0').value);
    let k = parseFloat(document.getElementById('k').value);
    let M = parseFloat(document.getElementById('M').value);
    let t = parseFloat(document.getElementById('t').value);
    let targetV = parseFloat(document.getElementById('targetV').value);

    let result = logisticGrowth(V0, k, M, t);
    let t_target = timeToReach(V0, k, M, targetV);

    document.getElementById('result').innerHTML =
        `V(t) = ${result.V.toFixed(2)} cm³<br>` +
        `dV/dt = ${result.dVdt.toFixed(3)} cm³/day<br>` +
        `Time to reach ${targetV} cm³ = ${t_target.toFixed(2)} days`;

    drawChart(V0, k, M);
}
