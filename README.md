zero = np.array([[1],[0]])
one = np.array([[0],[1]])
X = np.array([[0,1],[1,0]])
print(np.dot(X,zero))
print(np.dot(X,one))
backend = 'ibmqx2' # the backend to run on
shots = 1024    # the number of shots in the experiment 

Q_program = QuantumProgram()
Q_program.set_api(Qconfig.APItoken, Qconfig.config['url']) # set the APIToken and API url


# Creating registers
qr = Q_program.create_quantum_register('qr', 1)
cr = Q_program.create_classical_register('cr', 1)

# Quantum circuit ground 
qc_ground = Q_program.create_circuit('ground', [qr], [cr])
qc_ground.measure(qr[0], cr[0])

# Quantum circuit excited 
qc_excited = Q_program.create_circuit('excited', [qr], [cr])
qc_excited.x(qr)
qc_excited.measure(qr[0], cr[0])


circuits = ['ground', 'excited']

Q_program.get_qasms(circuits)
result = Q_program.execute(circuits, backend=backend, shots=shots, max_credits=3, wait=10, timeout=240, silent=False)
plot_histogram(result.get_counts('ground'))
