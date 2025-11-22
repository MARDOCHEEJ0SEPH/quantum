# Module 04C: Quantum Forex Optimization

## DV-CV Quantum Computing for Currency Markets

**Part of Universal Rebalancing Theory by Mardochée JOSEPH**

---

## Learning Objectives

By the end of this chapter, you will:
- Apply quantum algorithms to forex portfolio optimization
- Implement carry trade optimization using QAOA
- Use CV states for central bank event modeling
- Build 24/5 quantum monitoring systems

---

## 4C.1 Forex Market Quantum Characteristics

### Currency Market Unique Properties

```
┌─────────────────────────────────────────────────────────────┐
│           FOREX MARKET QUANTUM OPPORTUNITIES                 │
├─────────────────────────────────────────────────────────────┐
│                                                             │
│  24/5 CONTINUOUS TRADING                                    │
│  • Tokyo → London → New York sessions                      │
│  • Continuous optimization required                         │
│  • Quantum parallel processing advantage                   │
│                                                             │
│  CARRY TRADE OPTIMIZATION                                   │
│  • Interest rate differentials across 28+ pairs            │
│  • Risk-adjusted carry calculation                         │
│  • QAOA for optimal carry portfolio                        │
│                                                             │
│  CENTRAL BANK EVENTS                                        │
│  • 8 major central banks                                   │
│  • Policy decisions = volatility spikes                    │
│  • CV squeezed states for event modeling                   │
│                                                             │
│  CURRENCY CORRELATIONS                                      │
│  • Complex correlation structure                           │
│  • USD as hub currency                                     │
│  • Entangled quantum states for correlation               │
│                                                             │
│  TRIANGULAR ARBITRAGE                                       │
│  • Grover search for arbitrage opportunities              │
│  • Microsecond execution required                         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Forex-Specific URT Variables

```
Extended URT for Forex:

E(R_forex,t) = Σₚ [Interest_Rate_p,t + Currency_Momentum_p,t - Carry_Cost_p,t]
             + Σₚ [PPP_Adjustment_p,t + Flow_Indicator_p,t]

Risk(Forex,t) = √(Currency_Volatility_t² + Central_Bank_Risk_t²
                + Geopolitical_Risk_t² + Liquidity_Risk_t²)

Cost(Forex,t) = Σₚ [Bid_Ask_Spread_p,t + Swap_Rates_p,t + Platform_Fees_p,t
                  + Slippage_p,t]

Constraints:
• Currency_Exposure(major) ≤ 30% for any single currency
• Correlation_Limit across pairs
• Leverage_Limit (typically 10:1 to 50:1)
• Central_Bank_Event buffer zones
```

---

## 4C.2 Quantum Hamiltonian for Forex

### Full Forex Hamiltonian

```
Ĥ_forex = Ĥ_carry + Ĥ_momentum + Ĥ_correlation + Ĥ_central_bank + Ĥ_spread

Components:

Ĥ_carry = -Σₚ interest_differential_p × Ẑₚ
          (Maximize carry income)

Ĥ_momentum = -Σₚ trend_score_p × Ẑₚ
             (Follow currency trends)

Ĥ_correlation = Σₚq correlation_pq × Ẑₚ × Ẑq
                (Manage correlation risk)

Ĥ_central_bank = Σₚ cb_risk_p × Ẑₚ²
                 (Penalize positions before CB events)

Ĥ_spread = Σₚ spread_p × |Ẑₚ|
           (Account for bid-ask spread)
```

### Currency Pair Encoding

```
Major pairs (7):
EUR/USD, USD/JPY, GBP/USD, USD/CHF, AUD/USD, USD/CAD, NZD/USD

Cross pairs (21):
EUR/GBP, EUR/JPY, EUR/CHF, EUR/AUD, EUR/CAD, EUR/NZD,
GBP/JPY, GBP/CHF, GBP/AUD, GBP/CAD, GBP/NZD,
CHF/JPY, AUD/JPY, CAD/JPY, NZD/JPY,
AUD/CHF, AUD/CAD, AUD/NZD, CAD/CHF, NZD/CHF, NZD/CAD

Total: 28 major/cross pairs

Quantum encoding:
|pair_p⟩ = |position⟩ ⊗ |size⟩ ⊗ |direction⟩

Position: 3 qubits (8 levels: 0%, 5%, 10%, 15%, 20%, 25%, 30%, 35%)
Direction: 1 qubit (0=long, 1=short)

Total for 28 pairs: 28 × 4 = 112 qubits
```

---

## 4C.3 QAOA for Carry Trade Optimization

### Carry Trade Problem

```
Carry trade: Borrow low-rate currency, invest in high-rate currency

Profit = Interest_differential × Position_size - (Spread + Swap_cost)

Risk: Currency moves can wipe out months of carry

Optimization: Maximize risk-adjusted carry across all pairs
```

### QAOA Carry Optimizer

```python
class QuantumCarryOptimizer:
    """
    QAOA optimization for carry trade portfolio
    """

    def __init__(self, currency_pairs):
        self.pairs = currency_pairs
        self.n_pairs = len(currency_pairs)

    def calculate_carry(self, pair):
        """
        Calculate carry for a currency pair
        """
        base_rate = self.get_interest_rate(pair['base'])
        quote_rate = self.get_interest_rate(pair['quote'])

        # Long position: receive base rate, pay quote rate
        long_carry = base_rate - quote_rate

        # Adjust for swap rates (can differ from pure rate differential)
        swap_adjustment = pair.get('swap_long', 0) / 365

        return long_carry + swap_adjustment

    def build_carry_hamiltonian(self, pairs_data, risk_aversion=1.0):
        """
        Build Hamiltonian for carry optimization

        H = -Σₚ carry_p × Z_p + λ Σₚq cov_pq × Z_p × Z_q
        """
        H = {}

        # Carry terms (negative = maximize)
        for p, pair in enumerate(pairs_data):
            carry = self.calculate_carry(pair)
            volatility = pair['volatility']

            # Risk-adjusted carry (Sharpe-like)
            risk_adjusted_carry = carry / (volatility + 0.01)
            H[(p,)] = -risk_adjusted_carry

        # Correlation/covariance terms
        for p in range(self.n_pairs):
            for q in range(p+1, self.n_pairs):
                correlation = self._get_pair_correlation(p, q, pairs_data)
                vol_p = pairs_data[p]['volatility']
                vol_q = pairs_data[q]['volatility']
                covariance = correlation * vol_p * vol_q

                H[(p, q)] = risk_aversion * covariance

        return H

    def _get_pair_correlation(self, p, q, pairs_data):
        """
        Get correlation between two pairs

        Pairs sharing currencies are correlated
        """
        pair_p = pairs_data[p]
        pair_q = pairs_data[q]

        shared_currencies = set([pair_p['base'], pair_p['quote']]) & \
                          set([pair_q['base'], pair_q['quote']])

        if len(shared_currencies) == 2:
            return 1.0  # Same pair
        elif len(shared_currencies) == 1:
            return 0.5  # Share one currency
        else:
            return 0.1  # No shared currency

    def qaoa_circuit(self, H, p_layers=4):
        """
        Build QAOA circuit for carry optimization
        """
        # 3 qubits per pair for position size
        n_qubits = self.n_pairs * 3

        qc = QuantumCircuit(n_qubits)

        gamma = [Parameter(f'γ_{l}') for l in range(p_layers)]
        beta = [Parameter(f'β_{l}') for l in range(p_layers)]

        # Initial superposition
        qc.h(range(n_qubits))

        for layer in range(p_layers):
            # Cost unitary
            for term, coeff in H.items():
                if len(term) == 1:
                    p = term[0]
                    for q in range(p*3, (p+1)*3):
                        qc.rz(2 * gamma[layer] * coeff, q)
                else:
                    p1, p2 = term
                    qc.cx(p1*3, p2*3)
                    qc.rz(2 * gamma[layer] * coeff, p2*3)
                    qc.cx(p1*3, p2*3)

            # Mixer
            for q in range(n_qubits):
                qc.rx(2 * beta[layer], q)

        return qc, gamma, beta

    def optimize_carry_portfolio(self, pairs_data):
        """
        Run full QAOA optimization
        """
        H = self.build_carry_hamiltonian(pairs_data)
        qc, gamma, beta = self.qaoa_circuit(H)

        # Initial parameters
        init_params = np.concatenate([
            np.random.uniform(0, np.pi, 4),  # gamma
            np.random.uniform(0, np.pi, 4)   # beta
        ])

        # Optimize (VQE-style)
        def cost_function(params):
            bound_qc = qc.assign_parameters({
                **{g: params[i] for i, g in enumerate(gamma)},
                **{b: params[4+i] for i, b in enumerate(beta)}
            })
            bound_qc.measure_all()

            simulator = AerSimulator()
            result = simulator.run(bound_qc, shots=1000).result()
            counts = result.get_counts()

            # Calculate expected cost
            exp_cost = 0
            for bitstring, count in counts.items():
                weights = self._decode_weights(bitstring)
                cost = self._portfolio_cost(weights, H)
                exp_cost += cost * count / 1000

            return exp_cost

        from scipy.optimize import minimize
        result = minimize(cost_function, init_params, method='COBYLA')

        return result

    def _decode_weights(self, bitstring):
        """Decode bitstring to portfolio weights"""
        weights = {}
        for p, pair in enumerate(self.pairs):
            bits = bitstring[p*3:(p+1)*3][::-1]
            weight = int(bits, 2) / 8  # 0-0.875 in 0.125 steps
            weights[pair['name']] = weight
        return weights

    def _portfolio_cost(self, weights, H):
        """Calculate portfolio cost"""
        cost = 0
        for term, coeff in H.items():
            if len(term) == 1:
                p = term[0]
                cost += coeff * list(weights.values())[p]
            else:
                p1, p2 = term
                cost += coeff * list(weights.values())[p1] * list(weights.values())[p2]
        return cost
```

---

## 4C.4 CV Quantum for Central Bank Events

### Central Bank Event Modeling

```
Central Banks:
• Fed (USD), ECB (EUR), BOJ (JPY), BOE (GBP)
• SNB (CHF), RBA (AUD), BOC (CAD), RBNZ (NZD)

Event types:
1. Rate decisions (scheduled)
2. Policy statements (scheduled)
3. Emergency announcements (unscheduled)
4. Minutes release (scheduled)

Impact:
• Pre-event: Volatility expansion (anticipation)
• Post-event: Price jump + vol crush or expansion
```

### CV Central Bank Circuit

```python
def cv_central_bank_model(pair_data, days_to_cb, expected_move):
    """
    Model currency pair around central bank event using CV

    Parameters:
    - pair_data: Current price, vol, etc.
    - days_to_cb: Days until central bank decision
    - expected_move: Market-implied move in pips
    """
    prog = sf.Program(3)  # price, implied_vol, direction_bias

    with prog.context as q:
        # Current price
        current_price = pair_data['price']
        ops.Coherent(current_price) | q[0]

        # Pre-event volatility dynamics
        if days_to_cb <= 1:
            # Day of event: maximum uncertainty
            # Use anti-squeezed state (expanded uncertainty)
            implied_vol = pair_data['implied_vol']
            ops.Sgate(-np.log(1 + implied_vol * 2)) | q[0]

            # Strong correlation with vol mode
            ops.S2gate(0.8) | (q[0], q[1])

        elif days_to_cb <= 7:
            # Week before: building uncertainty
            vol_factor = 1 + (7 - days_to_cb) / 7 * implied_vol
            ops.Sgate(-np.log(vol_factor)) | q[0]

            ops.S2gate(0.4) | (q[0], q[1])

        else:
            # Normal regime
            ops.Sgate(np.log(1 + pair_data['historical_vol'])) | q[0]

        # Implied volatility mode
        ops.Coherent(pair_data['implied_vol']) | q[1]

        # Direction bias (market positioning)
        market_bias = pair_data.get('positioning_bias', 0)
        ops.Coherent(market_bias) | q[2]
        ops.BSgate(0.2) | (q[0], q[2])  # Price affected by positioning

        # Measurements
        ops.MeasureX | q[0]
        ops.MeasureX | q[1]
        ops.MeasureX | q[2]

    return prog


def simulate_cb_scenarios(pair, cb_event, n_scenarios=1000):
    """
    Simulate currency scenarios around central bank event
    """
    scenarios = {
        'hawkish': [],
        'dovish': [],
        'as_expected': [],
        'shock': []
    }

    # Define scenario parameters
    scenario_params = {
        'hawkish': {'price_move': 0.01, 'vol_change': -0.2},
        'dovish': {'price_move': -0.01, 'vol_change': -0.2},
        'as_expected': {'price_move': 0.0, 'vol_change': -0.3},
        'shock': {'price_move': 0.03, 'vol_change': 0.5}
    }

    for scenario_name, params in scenario_params.items():
        prog = sf.Program(2)

        with prog.context as q:
            # Post-event price
            new_price = pair['price'] * (1 + params['price_move'])
            ops.Coherent(new_price) | q[0]

            # Post-event volatility
            new_vol = pair['implied_vol'] * (1 + params['vol_change'])
            if params['vol_change'] < 0:
                # Vol crush: more squeezed
                ops.Sgate(0.3) | q[0]
            else:
                # Vol expansion: anti-squeezed
                ops.Sgate(-0.3) | q[0]

            ops.Coherent(new_vol) | q[1]

            ops.MeasureX | q[0]
            ops.MeasureX | q[1]

        eng = sf.Engine("gaussian")

        for _ in range(n_scenarios // 4):
            result = eng.run(prog)
            scenarios[scenario_name].append({
                'price': result.samples[0][0],
                'vol': result.samples[0][1]
            })

    return scenarios


class CentralBankRiskManager:
    """
    Manage forex positions around central bank events
    """

    def __init__(self, central_banks):
        self.cbs = central_banks
        self.calendar = self._load_calendar()

    def _load_calendar(self):
        """Load central bank calendar"""
        return {
            'Fed': {'next_meeting': '2025-01-29', 'pairs': ['EUR/USD', 'USD/JPY', 'GBP/USD']},
            'ECB': {'next_meeting': '2025-01-30', 'pairs': ['EUR/USD', 'EUR/GBP', 'EUR/JPY']},
            'BOJ': {'next_meeting': '2025-01-24', 'pairs': ['USD/JPY', 'EUR/JPY', 'GBP/JPY']},
            'BOE': {'next_meeting': '2025-02-06', 'pairs': ['GBP/USD', 'EUR/GBP', 'GBP/JPY']},
        }

    def assess_cb_risk(self, portfolio, current_date):
        """
        Assess central bank event risk for portfolio
        """
        risk_assessment = {}

        for cb, info in self.calendar.items():
            meeting_date = datetime.strptime(info['next_meeting'], '%Y-%m-%d')
            days_to_meeting = (meeting_date - current_date).days

            if days_to_meeting <= 0:
                continue

            # Check portfolio exposure to affected pairs
            affected_exposure = 0
            for pair in info['pairs']:
                affected_exposure += abs(portfolio.get(pair, 0))

            risk_level = 'high' if days_to_meeting <= 2 and affected_exposure > 0.2 else \
                        'medium' if days_to_meeting <= 7 and affected_exposure > 0.1 else \
                        'low'

            risk_assessment[cb] = {
                'days_to_meeting': days_to_meeting,
                'affected_exposure': affected_exposure,
                'risk_level': risk_level,
                'recommendation': self._get_recommendation(risk_level, affected_exposure)
            }

        return risk_assessment

    def _get_recommendation(self, risk_level, exposure):
        """Get risk management recommendation"""
        if risk_level == 'high':
            return f"Reduce exposure by {min(exposure * 0.5, 0.15):.1%} or hedge with options"
        elif risk_level == 'medium':
            return "Monitor closely, consider reducing if vol spikes"
        else:
            return "Normal positioning acceptable"
```

---

## 4C.5 Currency Correlation Modeling

### Entangled Currency States

```
USD as hub currency creates entanglement:

|forex_state⟩ = |USD_strength⟩ ⊗ (Σ_c α_c |currency_c⟩)

When USD strengthens, all USD pairs move together
This is quantum-like correlation!

Model with multi-mode CV entanglement:
|Ω_forex⟩ = TMS(r_EUR) ⊗ TMS(r_JPY) ⊗ ... |USD⟩

Where TMS = Two-Mode Squeeze creates correlation
```

### Quantum Correlation Engine

```python
class QuantumForexCorrelation:
    """
    Model forex correlations using CV quantum states
    """

    def __init__(self, pairs):
        self.pairs = pairs
        self.n_pairs = len(pairs)

    def build_correlation_state(self, correlation_matrix):
        """
        Create CV state encoding currency correlations
        """
        prog = sf.Program(self.n_pairs)

        # Eigendecomposition of correlation matrix
        eigenvalues, eigenvectors = np.linalg.eigh(correlation_matrix)

        with prog.context as q:
            # Create squeezed states based on eigenvalues
            for i, eigval in enumerate(eigenvalues):
                if eigval > 1:
                    # Positive correlation: squeeze
                    r = np.log(eigval) / 2
                    ops.Sgate(r) | q[i]
                elif eigval < 1 and eigval > 0:
                    # Negative correlation: anti-squeeze
                    r = -np.log(eigval) / 2
                    ops.Sgate(r, np.pi/2) | q[i]

            # Apply interferometer to mix according to eigenvectors
            ops.Interferometer(eigenvectors) | q

        return prog

    def measure_correlation_state(self, prog, shots=1000):
        """
        Measure correlation state to get correlated samples
        """
        # Add measurements
        with prog.context as q:
            for i in range(self.n_pairs):
                ops.MeasureX | q[i]

        eng = sf.Engine("gaussian")
        result = eng.run(prog, shots=shots)

        return result.samples

    def simulate_correlated_moves(self, correlation_matrix, n_scenarios=1000):
        """
        Simulate correlated currency moves using quantum
        """
        prog = self.build_correlation_state(correlation_matrix)

        # Add measurement
        with prog.context as q:
            for i in range(self.n_pairs):
                ops.MeasureX | q[i]

        eng = sf.Engine("gaussian")
        scenarios = []

        for _ in range(n_scenarios):
            result = eng.run(prog)
            scenarios.append(result.samples[0])

        return np.array(scenarios)

    def real_time_correlation_update(self, new_returns):
        """
        Update correlation matrix in real-time using quantum tomography
        """
        # Simplified: Exponential moving average update
        alpha = 0.1
        self.correlation_matrix = (1 - alpha) * self.correlation_matrix + \
                                  alpha * np.outer(new_returns, new_returns)

        return self.correlation_matrix


def triangular_correlation_check(pair1, pair2, pair3):
    """
    Check triangular relationship for arbitrage
    e.g., EUR/USD × USD/JPY should equal EUR/JPY
    """
    # This is a consistency check
    # Quantum speedup in finding violations

    implied = pair1['rate'] * pair2['rate']
    actual = pair3['rate']

    discrepancy = abs(implied - actual) / actual

    return {
        'implied': implied,
        'actual': actual,
        'discrepancy': discrepancy,
        'arbitrage_opportunity': discrepancy > 0.0001  # 1 pip threshold
    }
```

---

## 4C.6 Grover Search for Triangular Arbitrage

### Triangular Arbitrage

```
Triangular arbitrage: Profit from rate inconsistencies

Example:
1. Start with 1,000,000 USD
2. Buy EUR: 1,000,000 / 1.10 = 909,090 EUR
3. Buy GBP: 909,090 × 0.85 = 772,727 GBP
4. Sell GBP: 772,727 × 1.30 = 1,004,545 USD

Profit: $4,545 (risk-free!)

Challenge: Opportunities last milliseconds
Quantum: Search faster than classical
```

### Grover Arbitrage Scanner

```python
class QuantumArbitrageScanner:
    """
    Grover-based triangular arbitrage detection
    """

    def __init__(self, pairs_rates):
        self.rates = pairs_rates
        self.triangles = self._enumerate_triangles()
        self.n_triangles = len(self.triangles)

    def _enumerate_triangles(self):
        """
        Enumerate all valid triangular combinations
        """
        currencies = set()
        for pair in self.rates:
            currencies.add(pair.split('/')[0])
            currencies.add(pair.split('/')[1])

        triangles = []
        currencies = list(currencies)

        for i, c1 in enumerate(currencies):
            for j, c2 in enumerate(currencies[i+1:], i+1):
                for c3 in currencies[j+1:]:
                    # Check if all three pairs exist
                    pairs = [f"{c1}/{c2}", f"{c2}/{c3}", f"{c3}/{c1}"]
                    alt_pairs = [f"{c2}/{c1}", f"{c3}/{c2}", f"{c1}/{c3}"]

                    valid = True
                    for p, ap in zip(pairs, alt_pairs):
                        if p not in self.rates and ap not in self.rates:
                            valid = False
                            break

                    if valid:
                        triangles.append((c1, c2, c3))

        return triangles

    def check_arbitrage(self, triangle):
        """
        Check if triangle has arbitrage opportunity
        """
        c1, c2, c3 = triangle

        # Get rates (handle both directions)
        def get_rate(base, quote):
            pair = f"{base}/{quote}"
            if pair in self.rates:
                return self.rates[pair]
            else:
                return 1 / self.rates[f"{quote}/{base}"]

        # Calculate round-trip
        # Start with 1 unit of c1
        amount = 1.0

        # c1 -> c2
        amount *= get_rate(c1, c2)

        # c2 -> c3
        amount *= get_rate(c2, c3)

        # c3 -> c1
        amount *= get_rate(c3, c1)

        profit = amount - 1.0

        return {
            'triangle': triangle,
            'round_trip': amount,
            'profit': profit,
            'profitable': profit > 0.0001  # 1 bp threshold
        }

    def build_arbitrage_oracle(self, threshold=0.0001):
        """
        Build Grover oracle that marks profitable triangles
        """
        n_qubits = int(np.ceil(np.log2(self.n_triangles)))

        oracle = QuantumCircuit(n_qubits + 1)

        # For each triangle index, check if profitable
        # Mark with phase flip if profitable

        # This is a simplified oracle
        # Full implementation requires encoding profit calculation

        for i, triangle in enumerate(self.triangles):
            result = self.check_arbitrage(triangle)

            if result['profitable']:
                # Mark this index
                binary = format(i, f'0{n_qubits}b')
                for j, bit in enumerate(binary):
                    if bit == '0':
                        oracle.x(j)

                oracle.mcx(list(range(n_qubits)), n_qubits)

                for j, bit in enumerate(binary):
                    if bit == '0':
                        oracle.x(j)

        return oracle

    def grover_scan(self):
        """
        Use Grover search to find arbitrage opportunities
        """
        n_qubits = int(np.ceil(np.log2(self.n_triangles)))

        qc = QuantumCircuit(n_qubits + 1, n_qubits)

        # Initialize
        qc.h(range(n_qubits))
        qc.x(n_qubits)
        qc.h(n_qubits)

        # Estimate number of solutions
        n_solutions = sum(1 for t in self.triangles if self.check_arbitrage(t)['profitable'])

        if n_solutions == 0:
            return []

        # Grover iterations
        n_iter = int(np.pi / 4 * np.sqrt(self.n_triangles / n_solutions))

        oracle = self.build_arbitrage_oracle()

        for _ in range(n_iter):
            # Oracle
            qc.compose(oracle, inplace=True)

            # Diffusion
            qc.h(range(n_qubits))
            qc.x(range(n_qubits))
            qc.h(n_qubits - 1)
            qc.mcx(list(range(n_qubits - 1)), n_qubits - 1)
            qc.h(n_qubits - 1)
            qc.x(range(n_qubits))
            qc.h(range(n_qubits))

        qc.measure(range(n_qubits), range(n_qubits))

        # Run and find opportunities
        simulator = AerSimulator()
        result = simulator.run(qc, shots=100).result()
        counts = result.get_counts()

        # Decode results
        opportunities = []
        for bitstring, count in counts.items():
            idx = int(bitstring[::-1], 2)
            if idx < self.n_triangles:
                triangle = self.triangles[idx]
                arb = self.check_arbitrage(triangle)
                if arb['profitable']:
                    arb['count'] = count
                    opportunities.append(arb)

        return sorted(opportunities, key=lambda x: -x['profit'])
```

---

## 4C.7 24/5 Quantum Monitoring System

### Session-Aware Optimization

```python
class Quantum24x5Monitor:
    """
    Continuous forex monitoring with session awareness
    """

    def __init__(self, portfolio):
        self.portfolio = portfolio
        self.sessions = {
            'tokyo': {'start': 0, 'end': 9, 'pairs': ['USD/JPY', 'EUR/JPY', 'AUD/JPY']},
            'london': {'start': 8, 'end': 17, 'pairs': ['EUR/USD', 'GBP/USD', 'EUR/GBP']},
            'new_york': {'start': 13, 'end': 22, 'pairs': ['EUR/USD', 'USD/CAD', 'USD/CHF']}
        }

    def get_active_sessions(self, utc_hour):
        """Get currently active trading sessions"""
        active = []
        for session, info in self.sessions.items():
            if info['start'] <= utc_hour < info['end']:
                active.append(session)
            elif info['start'] > info['end']:  # Crosses midnight
                if utc_hour >= info['start'] or utc_hour < info['end']:
                    active.append(session)
        return active

    def session_adjusted_optimization(self, market_data, utc_hour):
        """
        Optimize based on active session
        """
        active_sessions = self.get_active_sessions(utc_hour)

        # Adjust volatility expectations
        vol_multiplier = 1.0
        if 'london' in active_sessions and 'new_york' in active_sessions:
            vol_multiplier = 1.3  # London/NY overlap = highest vol
        elif len(active_sessions) == 0:
            vol_multiplier = 0.5  # Weekend/gap

        # Adjust liquidity for session-relevant pairs
        liquidity_boost = {}
        for session in active_sessions:
            for pair in self.sessions[session]['pairs']:
                liquidity_boost[pair] = liquidity_boost.get(pair, 1.0) * 1.2

        # Run quantum optimization with adjustments
        adjusted_data = self._apply_session_adjustments(
            market_data, vol_multiplier, liquidity_boost
        )

        return self._run_quantum_optimization(adjusted_data)

    def _apply_session_adjustments(self, data, vol_mult, liq_boost):
        """Apply session-specific adjustments"""
        adjusted = data.copy()

        for pair in adjusted['pairs']:
            pair['volatility'] *= vol_mult
            pair['liquidity'] *= liq_boost.get(pair['name'], 1.0)
            pair['spread'] /= liq_boost.get(pair['name'], 1.0)

        return adjusted

    def _run_quantum_optimization(self, data):
        """Run QAOA optimization"""
        optimizer = QuantumCarryOptimizer(data['pairs'])
        return optimizer.optimize_carry_portfolio(data['pairs'])


class RealTimeForexQuantum:
    """
    Real-time forex optimization system
    """

    def __init__(self, config):
        self.config = config
        self.carry_optimizer = QuantumCarryOptimizer(config['pairs'])
        self.correlation_engine = QuantumForexCorrelation(config['pairs'])
        self.arb_scanner = QuantumArbitrageScanner(config['rates'])
        self.cb_manager = CentralBankRiskManager(config['central_banks'])
        self.monitor = Quantum24x5Monitor(config['initial_portfolio'])

    def run_optimization_cycle(self, market_data, current_time):
        """
        Run complete optimization cycle
        """
        results = {}

        # 1. Session-aware base optimization
        utc_hour = current_time.hour
        session_opt = self.monitor.session_adjusted_optimization(market_data, utc_hour)
        results['session_optimization'] = session_opt

        # 2. Carry trade optimization
        carry_weights = self.carry_optimizer.optimize_carry_portfolio(market_data['pairs'])
        results['carry_weights'] = carry_weights

        # 3. Correlation analysis
        corr_scenarios = self.correlation_engine.simulate_correlated_moves(
            market_data['correlation_matrix'], n_scenarios=500
        )
        results['correlation_scenarios'] = corr_scenarios

        # 4. Arbitrage scan
        arb_opportunities = self.arb_scanner.grover_scan()
        results['arbitrage'] = arb_opportunities

        # 5. Central bank risk
        cb_risk = self.cb_manager.assess_cb_risk(
            self.monitor.portfolio,
            current_time.date()
        )
        results['central_bank_risk'] = cb_risk

        # 6. Combine into final recommendation
        final_weights = self._combine_signals(
            session_opt, carry_weights, corr_scenarios, cb_risk
        )
        results['recommended_weights'] = final_weights

        return results

    def _combine_signals(self, session, carry, correlation, cb_risk):
        """Combine various optimization signals"""
        # Simplified combination
        # In practice, use more sophisticated blending

        combined = {}

        # Start with carry weights
        for pair, weight in carry.items():
            combined[pair] = weight

        # Adjust for CB risk
        for cb, risk in cb_risk.items():
            if risk['risk_level'] == 'high':
                for pair in risk.get('affected_pairs', []):
                    if pair in combined:
                        combined[pair] *= 0.5

        # Normalize
        total = sum(abs(w) for w in combined.values())
        if total > 0:
            combined = {k: v/total for k, v in combined.items()}

        return combined
```

---

## 4C.8 Complete Forex Quantum System

```python
class QuantumForexPortfolioManager:
    """
    Complete quantum forex portfolio management system
    """

    def __init__(self, config):
        self.config = config
        self.system = RealTimeForexQuantum(config)

    def full_optimization(self, market_data):
        """
        Run complete forex optimization
        """
        from datetime import datetime
        current_time = datetime.utcnow()

        return self.system.run_optimization_cycle(market_data, current_time)
```

---

## 4C.9 Summary

### Key Forex Quantum Equations

```
Carry Trade QAOA:
Ĥ_carry = -Σₚ (r_base - r_quote)ₚ × Ẑₚ + λ Σₚq Covₚq × ẐₚẐq

Central Bank CV Model:
|pre_CB⟩ = S(-r_uncertainty)|price⟩
|post_CB⟩ = D(policy_surprise)S(±r_vol)|price⟩

Correlation Entanglement:
|forex⟩ = Πₚ TMS(rₚ)|currencies⟩

Triangular Arbitrage (Grover):
|arb*⟩ = Grover^k(Oracle_profit > 0)|triangles⟩
```

### Forex Quantum Advantages

| Task | Classical | Quantum | Advantage |
|------|-----------|---------|-----------|
| Carry optimization | O(n!) | O(√n!) | Exponential |
| Triangular arb scan | O(n³) | O(n^1.5) | Quadratic |
| Correlation simulation | Monte Carlo | CV sampling | Native |
| CB scenario analysis | Grid search | CV states | Continuous |

---

## Next Chapter

[Chapter 4D: Quantum Bonds and Commodities →](04d-bonds-commodities-quantum.md)
