//Package name
package ca.uwaterloo.lab2_203_27;

//Package imports
import java.util.Arrays;

import com.example.lab2_203_27.R;

import android.app.Activity;
import android.hardware.Sensor;
import android.hardware.SensorEvent;
import android.hardware.SensorEventListener;
import android.hardware.SensorManager;
import android.os.Bundle;
import android.util.Log;
import android.view.Menu;
import android.view.View;
import android.widget.Button;
import android.widget.LinearLayout;
import android.widget.TextView;
import android.view.View.OnClickListener;

public class MainActivity extends Activity implements OnClickListener {

	//Linear Layout is declared
	LinearLayout L1;
	
	//Labels are declared
	TextView STEP;
	TextView labelACC;
	
	//Variable declaration to track steps
	public int StepCount;
	
	//Boolean variable to track current phase in a step
	public boolean up = true;
	public boolean down = false;
	
	//Graph variable
	LineGraphView graph;

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		//Content View
		setContentView(R.layout.activity_main);
		
		//Resets the pedometer
		Button ResetB = new Button(getApplicationContext());
		
		//Creates a Linear Layout
		L1 = (LinearLayout) findViewById(R.id.L1);
		L1.setOrientation(LinearLayout.VERTICAL);
		
		//Initializes a graph that uses x, y, z, coordinates
		graph = new LineGraphView(getApplicationContext(), 100, Arrays.asList(
				"x", "y", "z"));
		//Adds a graph unto the linear layout
		L1.addView(graph);
		//Initializes the graph to be visible
		graph.setVisibility(View.VISIBLE);
		
		//Adds a reset button to clear the graph
		L1.addView(ResetB);
		
		//Sets the set for the reset button
		ResetB.setText("Reset");
		//Event (Click) listener for the Reset button
		ResetB.setOnClickListener(this);
		
		//Step text label
		STEP = (TextView) findViewById(R.id.STEP);
		
		//Acceleration text label
		labelACC = new TextView(getApplicationContext());
		//Assigns the current value of the acceleration to a text label
		labelACC.setText(" (Current Value):");
		
		//Creates a new sensor manager
		SensorManager sensorManager = (SensorManager) getSystemService(SENSOR_SERVICE);
		
		//Assigns the sensor manager to a new linear acceleration sensor
		Sensor LA = sensorManager
				.getDefaultSensor(Sensor.TYPE_LINEAR_ACCELERATION);

		//Creates a new event listener
		SensorEventListener LAE = new AccSensorEventListener(labelACC);

		sensorManager.registerListener(LAE, LA,
				SensorManager.SENSOR_DELAY_FASTEST);

	}

	@Override
	//Creates a method susceptible to clicks and resets step count to 0
	public void onClick(View v) {
		//Loops through three times and steps StepCount to 0.
		for (int i = 0; i < 3; i++) {
			StepCount = 0;
		}
	}

	@Override
	//Method created to inflate the Options Menu
	public boolean onCreateOptionsMenu(Menu menu) {
		// Inflate the menu; this adds items to the action bar if it is present.
		getMenuInflater().inflate(R.menu.main, menu);
		return true;
	}

	//Accelerator Event Listener Class
	class AccSensorEventListener implements SensorEventListener {
		
		//Declares a Textview label
		TextView output;

		// Outputs a TextView
		public AccSensorEventListener(TextView outputView) {
			output = outputView;
		}

		// When accuracy is changed
		@Override
		public void onAccuracyChanged(Sensor s, int i) {
		}

		// When sensor values change
		@Override
		public void onSensorChanged(SensorEvent se) {

			// Sets the current values for the accelerometer
			if (se.sensor.getType() == Sensor.TYPE_LINEAR_ACCELERATION) {
				
				// Creates a new array that is filtered 
				float[] cleanArray = lowpass(se.values, .2f, 0.95f);
				
				//Sets the low pass z-coordinate constant
				float lowpassZ = cleanArray[2];
				
				//Sets the text
				STEP.setText(String.format("STEPS %d", StepCount));

				//Sets the magnitude of the vector by taking the square root of (x^2 + y^2 + z^2)
				double magn = Math.sqrt(Math.pow(cleanArray[0], 2)
						+ Math.pow(cleanArray[1], 2)
						+ Math.pow(cleanArray[2], 2));
				
				//Finite state machine with the low pass constant and magnitude as input
				FSM(lowpassZ, magn);
				
				//Passes the value of the current magnitude to the 
				float magn1 = (float) magn;
				
				//Adds the value of the cleanArray as a point on the graph
				graph.addPoint(cleanArray);
				
				//Debugging uses
				Log.d("Magnitude", String.format("%f", magn1));

			}

		}
	}

	//Finite state machine
	public void FSM(float Z, double magn) {
		//Checks if the user is in the upwards rising phase of a step
		if (up == true && down == false) {
			//If the height is greater than 0.2 and vector magnitude is slowly than 0.3, then the user is in the downwards stepping phase of a step
			if (Z > 0.2 && magn < 0.3) {
				up = false;
				down = true;
			}
		}
		//If the user is in the downwards stepping phase
		if (up == false && down == true) {
			//If the height is smaller than -0.2, then the user has made a step and will proceed to step upwards
			if (Z < -0.2) {
				StepCount++;
				up = true;
				down = false;
			}
		}

	}

	//Low pass
	float[] lowpass(float[] in, float dt, float RC) {
		//Creates a float array out the same length as in
		float[] out = new float[in.length];
		//Creates a float alpha, variable depending on time and Resist-Capacitance 
		float alpha = dt / (dt + RC);
		//Sets the first value to be 0
		out[0] = 0;
		//Initializes the out array
		for (int i = 1; i < in.length; i++) {
			out[i] = alpha * in[i] + (1 - alpha) * out[i - 1];
		}
		//Returns the array
		return out;
	}
	//High pass
	float[] highpass(float[] in, float dt, float RC) {
		//Creates a float array out the same length as in
		float[] out = new float[in.length];
		//Creates a float alpha, variable depending on time and Resist-Capacitance 
		float alpha = RC / (dt + RC);
		//Sets the first value to be 0
		out[0] = 0;
		//Initializes the out array
		for (int i = 1; i < in.length; i++) {
			out[i] = alpha * out[i - 1] + alpha * (in[i] - in[i - 1]);
		}
		//Returns the array
		return out;
	}
}
