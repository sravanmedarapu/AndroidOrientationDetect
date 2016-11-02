# AndroidOrientationDetect
Android Faceup and Facedown orientation detection using Sensor



    private float mGZ = 0;//gravity acceleration along the z axis
    private int mEventCountSinceGZChanged = 0;
    private static final int MAX_COUNT_GZ_CHANGE = 10;

    private SensorEventListener orientationListener = new SensorEventListener() {

        @Override
        public void onAccuracyChanged(Sensor arg0, int arg1) {

        }

        @Override
        public void onSensorChanged(SensorEvent sensorEvent) {
            if (sensorEvent.sensor.getType() == Sensor.TYPE_ACCELEROMETER) {
                /**
                 * values[0]: Acceleration minus Gx on the x-axis
                 * values[1]: Acceleration minus Gy on the y-axis
                 * values[2]: Acceleration minus Gz on the z-axis
                 */
                float gz = sensorEvent.values[2];
                if (mGZ == 0) {
                    mGZ = gz;
                } else {
                    if ((mGZ * gz) < 0) {
                        mEventCountSinceGZChanged++;
                        if (mEventCountSinceGZChanged == MAX_COUNT_GZ_CHANGE) {
                            mGZ = gz;
                            mEventCountSinceGZChanged = 0;
                            if (gz > 0) {
                                Logger.debug("[AppiumUiAutomator2Server]", "Left side of the phone is Up!");
                            } else if (gz < 0) {
                                Logger.debug("[AppiumUiAutomator2Server]", "Left side of the phone is down!");
                            }
                        }
                    } else {
                        if (mEventCountSinceGZChanged > 0) {
                            mGZ = gz;
                            mEventCountSinceGZChanged = 0;
                        }
                    }
                }
            }
        }
    };


SensorEventListener registration

            Context ctx = InstrumentationRegistry.getInstrumentation().getContext();
            SensorManager sensorManager = (SensorManager) ctx.getSystemService(SENSOR_SERVICE);
            Sensor sensor =null;
            List<Sensor> sensorList = sensorManager.getSensorList(Sensor.TYPE_ACCELEROMETER);
            if (sensorList.size() > 0) {
                sensor = sensorList.get(0);
            }
            sensorManager.registerListener(orientationListener,sensor, 0, null);

