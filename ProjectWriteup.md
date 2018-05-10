# The C++ Project Writeup #

This is the writeup for the C++ Controls project.



Below are the notes on each of the required sections for this project.

## GenerateMotorCommands ##

From class lessons and Slack forum post from Mike Hahn, I use this formulas to get the desired thrusts

     F_tot = F0 + F1 + F2 + F3 = collThrustCmd
     tau_x = (F0 -  F1 + F2 + F3) * l = momentCmd.x
     tau_y = (F0 +  F1 - F2 - F3) * l = momentCmd.y
     tau_z = (-F0 + F1 + F2 - F3) * kappa = momentCmd.z
     
     l = L/ sqrt(2);
     We know that:
     F_x = tau_x / L
     F_y = tau_y / L
     F_z = - tau_z /kappa;
     And from Mike Hahn post on how to solve the above equaiton we get:
     F1 = 1/4 (A + B + C + D)
     F2 = 1/4 (A - B + C - D)
     F3 = 1/4 (A - B - C + D)
     F4 = 1/4 (A + B - C - D)
     where A = F_total, B = F_x, C= F_y and D = F_z

     The I just assigned the formula for the thrust of each rotor:

    cmd.desiredThrustsN[0] =(F_x + F_y + F_z + collThrustCmd)/4.f; // front left
    cmd.desiredThrustsN[1] = (-F_x + F_y - F_z + collThrustCmd)/4.f; // front right
    cmd.desiredThrustsN[2] = (F_x - F_y - F_z + collThrustCmd)/4.f; // rear left
    cmd.desiredThrustsN[3] = (-F_x - F_y + F_z + collThrustCmd)/4.f; // rear right

### Body Rate Control ###

This is a fairly simple control, where I mutluply the Inertia (x, y, z) components * the PQRError * kpPQR varaible.
And that yields the momentCmd.

momentCmd = Inertia * kpPQR * (pqrCmd - pqr);


### RollPitchControl, altitudeControl and YawControl ###

These controls are very similar to what we did in Excercise 5 of the lessosn. Intead of using arrays, I used a V3F when appropiate.

### LateralPositionControl ###

Here, instead of doing calculations for each x, y component I used the V3F directly. It is an efficient way to calculate it.
I constrained velociy using maxSpeedXY:

  velCmd.x = CONSTRAIN(velCmd.x, -maxSpeedXY, maxSpeedXY);
  velCmd.y = CONSTRAIN(velCmd.y, -maxSpeedXY, maxSpeedXY);

  And the final acceleration:

   accelCmd.x = CONSTRAIN(commanded.x, -maxAccelXY, maxAccelXY);
   accelCmd.y  = CONSTRAIN(commanded.y, -maxAccelXY, maxAccelXY);


All scenarios pass in my program.