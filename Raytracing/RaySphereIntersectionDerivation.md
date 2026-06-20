# 00 - VARIABLE DEFINITIONS
```
- WP : WORLD POSITION
- RO : RAY ORIGIN
- RD : RAY DIRECTION
- ID : INTERSECTION DISTANCE
- SP : SPHERE POSITION
- SR : SPHERE RADIUS
- RP : RELATIVE POSITION
- QC : QUADRATIC COEFFICIENT
- LC : LINEAR COEFFICIENT
- CC : CONSTANT COEFFICIENT
- NC : NUMERIC CONSTANT
```
# 01 - RAY DEFINITION
Express the intersection point as a position along the ray with a direction.
```
WP.xyz = RO.xyz + RD.xyz * ID;
```
Expand the vector equation into its individual components.
```
WP.x = RO.x + RD.x * ID;
WP.y = RO.y + RD.y * ID;
WP.z = RO.z + RD.z * ID;
```
# 02 - SPHERE DEFINITION
Replace the ray coordinates into the sphere equation.
```
(WP.x - SP.x)^2.0 + (WP.y - SP.y)^2.0 + (WP.z - SP.z)^2.0 = (SR)^2.0;
```
# 03 - SPHERE SUBSTITUTION
Substitute the ray coordinates into the sphere equation.
```
WP.x = RO.x + RD.x * ID;
WP.y = RO.y + RD.y * ID;
WP.z = RO.z + RD.z * ID;
```
Rewrite the sphere equation using only ray parameters.
```
(RO.x + RD.x * ID - SP.x)^2.0 + (RO.y + RD.y * ID - SP.y)^2.0 + (RO.z + RD.z * ID - SP.z)^2.0 = (SR)^2.0;
```
# 04 - RELATIVE VECTOR DEFINITION
Define the offset between the ray origin and sphere center.
```
RP.xyz = RO.xyz - SP.xyz;
```
Expand the relative vector into its indicvidual components.
```
RP.x = RO.x - SP.x;
RP.y = RO.y - SP.y;
RP.z = RO.z - SP.z;
```
```
(RP.x + RD.x * ID)^2.0 + (RP.y + RD.y * ID)^2.0 + (RP.z + RD.z * ID)^2.0 = (SR)^2.0;
```
# 05 - EXPANSION OF THE QUADRATIC FORM
Expand each squared term.
```
(RP.x)^2.0 + (RD.x * ID)^2.0 + 2.0 * RP.x * RD.x * ID + (RP.y)^2.0 + (RD.y * ID)^2.0 + 2.0 * RP.y * RD.y * ID + (RP.z)^2.0 + (RD.z * ID)^2.0 + 2.0 * RP.z * RD.z * ID = (SR)^2.0;
```
Group terms by powers of the intersection distance.
```
((RD.x)^2.0 + (RD.y)^2.0 + (RD.z)^2.0) * (ID)^2.0 + 2.0 * (RP.x * RD.x + RP.y * RD.y + RP.z * RD.z) * ID + (RP.x)^2.0 + (RP.y)^2.0 + (RP.z)^2.0 - (SR)^2.0 = 0.0;
```
Rewrite the expression using vector dot products.
```
(RD.xyz · RD.xyz) * (ID)^2.0 + 2.0 * (RP.xyz · RD.xyz) * ID + (RP.xyz · RP.xyz) - (SR)^2.0 = 0.0;
```
# 06 - COEFFICIENT DEFINITIONS
Identify the quadratic, linear and constant coefficients.
```
QC = (RD.xyz · RD.xyz);
LC = 2.0 * (RP.xyz · RD.xyz);
CC = (RP.xyz · RP.xyz) - (SR)^2.0;
```
# 07 - QUADRATIC EQUATION DEFINITION
Rewrite the intersection condition using the defined coefficients.
```
QC * (ID)^2.0 + LC * ID + CC = 0.0;
(QC * (ID)^2.0 + LC * ID + CC) / QC = 0.0;
(ID)^2.0 + (LC / QC) * ID + CC / QC = 0.0;
```
# 08 - COMPLETING THE SQUARE
Start from the general perfect-square identitity.
```
(ID + NC)^2.0 = (ID)^2.0 + 2.0 * NC * ID + (NC)^2.0;
```
Determine the value required to match the linear term.
```
2.0 * NC * ID = (LC / QC) * ID;
2.0 * NC = LC / QC;
NC = LC / (2.0 * QC);
```
Transform the quadratic equation into perfect-square form.
```
(ID)^2.0 + 2.0 * NC * ID + CC / QC = 0.0;
(ID)^2.0 + 2.0 * NC * ID = -CC / QC;
(ID + NC)^2.0 = -CC / QC + (NC)^2.0;
```
# 09 - FINAL SOLUTION
Take the square root of both sides.
```
ID + NC = -(-CC / QC + (NC)^2.0)^0.5;
ID + NC = +(-CC / QC + (NC)^2.0)^0.5;
```
Isolate the intersection distance.
```
ID = -NC - (-CC / QC + (NC)^2.0)^0.5;
ID = -NC + (-CC / QC + (NC)^2.0)^0.5;
```
Replace the completion constant with its definition.
```
ID = -(LC / (2.0 * QC)) - ((LC / (2.0 * QC))^2.0 - CC / QC)^0.5;
ID = -(LC / (2.0 * QC)) + ((LC / (2.0 * QC))^2.0 - CC / QC)^0.5;
```
Simplify to the standard quadratic formula.
```
ID = (-LC - (LC^2.0 - 4.0 * QC * CC)^0.5) / (2.0 * QC);
ID = (-LC + (LC^2.0 - 4.0 * QC * CC)^0.5) / (2.0 * QC);
```
# SOURCE CODE
```
const float UnfoundIntersection = -1.0;

struct source { vec3 position; vec3 direction; };
struct sphere { vec3 position; float radius; };

float getSphereIntersection(in source sampleSource, in sphere sampleSphere, in float minimumIntersection, in float maximumIntersection)
{
    vec3 relativePosition = sampleSource.position - sampleSphere.position;

    float quadraticCoefficient = dot(sampleSource.direction, sampleSource.direction);
    float linearCoefficient = 2.0 * dot(relativePosition, sampleSource.direction);
    float constantCoefficient = dot(relativePosition, relativePosition) - sampleSphere.radius * sampleSphere.radius;
    float quadraticDiscriminant = linearCoefficient * linearCoefficient - 4.0 * quadraticCoefficient * constantCoefficient;

    if (quadraticDiscriminant < 0.0) return UnfoundIntersection;

    float discriminantRadical = sqrt(quadraticDiscriminant);

    float negativeIntersection = (-linearCoefficient - discriminantRadical) / (2.0 * quadraticCoefficient);
    float positiveIntersection = (-linearCoefficient + discriminantRadical) / (2.0 * quadraticCoefficient);
    float incomingIntersection = min(negativeIntersection, positiveIntersection);
    float outgoingIntersection = max(negativeIntersection, positiveIntersection);

    if (incomingIntersection > minimumIntersection && incomingIntersection < maximumIntersection) return incomingIntersection;
    if (outgoingIntersection > minimumIntersection && outgoingIntersection < maximumIntersection) return outgoingIntersection;

    return UnfoundIntersection;
}
```
