====================
====================

# STEP 00 - VARIABLE DEFINITIONS

## STEP 01 - RAY DEFINITION

- WP : WORLD POSITION
- RO : RAY ORIGIN
- RD : RAY DIRECTION
- ID : INTERSECTION DISTANCE

## STEP 02 - SPHERE DEFINITION

- WP : WORLD POSITION
- SP : SPHERE POSITION
- SR : SPHERE RADIUS

## STEP 03 - SPHERE SUBSTITUTION

- WP : WORLD POSITION
- RO : RAY ORIGIN
- RD : RAY DIRECTION
- ID : INTERSECTION DISTANCE
- SP : SPHERE POSITION
- SR : SPHERE RADIUS

## STEP 04 - RELATIVE VECTOR DEFINITION

- RP : RELATIVE POSITION
- RO : RAY ORIGIN
- SP : SPHERE POSITION
- RD : RAY DIRECTION
- ID : INTERSECTION DISTANCE
- SR : SPHERE RADIUS

## STEP 05 - EXPANSION OF THE QUADRATIC FORM

- RP : RELATIVE POSITION
- RD : RAY DIRECTION
- ID : INTERSECTION DISTANCE
- SR : SPHERE RADIUS

## STEP 06 - COEFFICIENT DEFINITIONS

- QC : QUADRATIC COEFFICIENT
- LC : LINEAR COEFFICIENT
- CC : CONSTANT COEFFICIENT
- RP : RELATIVE POSITION
- RD : RAY DIRECTION
- SR : SPHERE RADIUS

## STEP 07 - QUADRATIC EQUATION DEFINITION

- QC : QUADRATIC COEFFICIENT
- LC : LINEAR COEFFICIENT
- CC : CONSTANT COEFFICIENT
- ID : INTERSECTION DISTANCE

## STEP 08 - COMPLETING THE SQUARE

- ID : INTERSECTION DISTANCE
- NC : NUMERIC CONSTANT
- LC : LINEAR COEFFICIENT
- QC : QUADRATIC COEFFICIENT

## STEP 09 - FINAL SOLUTION

- ID : INTERSECTION DISTANCE
- NC : NUMERIC CONSTANT
- CC : CONSTANT COEFFICIENT
- QC : QUADRATIC COEFFICIENT
- LC : LINEAR COEFFICIENT

====================
====================

# STEP 01 - RAY DEFINITION

WP.xyz = RO.xyz + RD.xyz * ID;

WP.x = RO.x + RD.x * ID;
WP.y = RO.y + RD.y * ID;
WP.z = RO.z + RD.z * ID;

====================
====================

# STEP 02 - SPHERE DEFINITION

(WP.x - SP.x)^2.0 + (WP.y - SP.y)^2.0 + (WP.z - SP.z)^2.0 = (SR)^2.0;

====================
====================

# STEP 03 - SPHERE SUBSTITUTION

WP.x = RO.x + RD.x * ID;
WP.y = RO.y + RD.y * ID;
WP.z = RO.z + RD.z * ID;

(RO.x + RD.x * ID - SP.x)^2.0 + (RO.y + RD.y * ID - SP.y)^2.0 + (RO.z + RD.z * ID - SP.z)^2.0 = (SR)^2.0;

====================
====================

# STEP 04 - RELATIVE VECTOR DEFINITION

RP.xyz = RO.xyz - SP.xyz;

RP.x = RO.x - SP.x;
RP.y = RO.y - SP.y;
RP.z = RO.z - SP.z;

(RP.x + RD.x * ID)^2.0 + (RP.y + RD.y * ID)^2.0 + (RP.z + RD.z * ID)^2.0 = (SR)^2.0;

====================
====================

# STEP 05 - EXPANSION OF THE QUADRATIC FORM

(RP.x)^2.0 + (RD.x * ID)^2.0 + 2.0 * RP.x * RD.x * ID + (RP.y)^2.0 + (RD.y * ID)^2.0 + 2.0 * RP.y * RD.y * ID + (RP.z)^2.0 + (RD.z * ID)^2.0 + 2.0 * RP.z * RD.z * ID = (SR)^2.0;

((RD.x)^2.0 + (RD.y)^2.0 + (RD.z)^2.0) * (ID)^2.0 + 2.0 * (RP.x * RD.x + RP.y * RD.y + RP.z * RD.z) * ID + (RP.x)^2.0 + (RP.y)^2.0 + (RP.z)^2.0 - (SR)^2.0 = 0.0;

(RD.xyz · RD.xyz) * (ID)^2.0 + 2.0 * (RP.xyz · RD.xyz) * ID + (RP.xyz · RP.xyz) - (SR)^2.0 = 0.0;

====================
====================

# STEP 06 - COEFFICIENT DEFINITIONS

QC = (RD.xyz · RD.xyz);

LC = 2.0 * (RP.xyz · RD.xyz);

CC = (RP.xyz · RP.xyz) - (SR)^2.0;

====================
====================

# STEP 07 - QUADRATIC EQUATION DEFINITION

QC * (ID)^2.0 + LC * ID + CC = 0.0;

(QC * (ID)^2.0 + LC * ID + CC) / QC = 0.0;

(ID)^2.0 + (LC / QC) * ID + CC / QC = 0.0;

====================
====================

# STEP 08 - COMPLETING THE SQUARE

(ID + NC)^2.0 = (ID)^2.0 + 2.0 * NC * ID + (NC)^2.0;

2.0 * NC * ID = (LC / QC) * ID;

2.0 * NC = LC / QC;

NC = LC / (2.0 * QC);

(ID)^2.0 + 2.0 * NC * ID + CC / QC = 0.0;

(ID)^2.0 + 2.0 * NC * ID = -CC / QC;

(ID + NC)^2.0 = -CC / QC + (NC)^2.0;

====================
====================

# STEP 09 - FINAL SOLUTION

ID + NC = +(-CC / QC + (NC)^2.0)^0.5;

ID + NC = -(-CC / QC + (NC)^2.0)^0.5;

ID = -NC + (-CC / QC + (NC)^2.0)^0.5;

ID = -NC - (-CC / QC + (NC)^2.0)^0.5;

ID = -(LC / (2.0 * QC)) + ((LC / (2.0 * QC))^2.0 - CC / QC)^0.5;

ID = -(LC / (2.0 * QC)) - ((LC / (2.0 * QC))^2.0 - CC / QC)^0.5;

ID = (-LC ± (LC^2.0 - 4.0 * QC * CC)^0.5) / (2.0 * QC);

====================
====================

# SOURCE CODE
```
struct source { vec3 position; vec3 direction; };

struct sphere { vec3 position; float radius; };

bool checkSphereIntersection(in source sampleSource, in sphere sampleSphere, out float sphereIntersection)
{
    vec3 relativePosition = sampleSource.position - sampleSphere.position;
    
    float quadraticCoefficient = dot(sampleSource.direction, sampleSource.direction);
    float linearCoefficient = 2.0 * dot(relativePosition, sampleSource.direction);
    float constantCoefficient = dot(relativePosition, relativePosition) - sampleSphere.radius * sampleSphere.radius;
    float quadraticDiscriminant = linearCoefficient * linearCoefficient - 4.0 * quadraticCoefficient * constantCoefficient;
    
    if (quadraticDiscriminant < 0.0) { sphereIntersection = 0.0; return false; }
    
    float discriminantRadical = sqrt(quadraticDiscriminant);
    float negativeIntersection = (-linearCoefficient - discriminantRadical) / (2.0 * quadraticCoefficient);
    float positiveIntersection = (-linearCoefficient + discriminantRadical) / (2.0 * quadraticCoefficient);
    float minimumIntersection = min(negativeIntersection, positiveIntersection);
    float maximumIntersection = max(negativeIntersection, positiveIntersection);
    
    if (minimumIntersection > 0.0) { sphereIntersection = minimumIntersection; return true; }
    if (maximumIntersection > 0.0) { sphereIntersection = maximumIntersection; return true; }
    
    sphereIntersection = 0.0;
    
    return false;
}
```
====================
====================