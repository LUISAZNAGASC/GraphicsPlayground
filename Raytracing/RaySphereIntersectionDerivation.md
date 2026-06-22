# 0 - VARIABLE DEFINITIONS
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
# 1 - RAY DEFINITION
```
WP = RO + RD * ID;
```
# 2 - SPHERE DEFINITION
```
(WP - SP) · (WP - SP) = (SR)^2.0;
```
# 3 - SPHERE SUBSTITUTION
```
WP = RO + RD * ID;
(RO + RD * ID - SP) · (RO + RD * ID - SP) = (SR)^2.0;
```
# 4 - RELATIVE VECTOR DEFINITION
```
RP = RO - SP;
```
```
(RP + RD * ID) · (RP + RD * ID) = (SR)^2.0;
```
# 5 - EXPANSION OF THE QUADRATIC FORM
```
(RP · RP) + 2.0 * (RP · RD) * ID + (RD · RD) * (ID)^2.0 = (SR)^2.0;
```
```
(RD · RD) * (ID)^2.0 + 2.0 * (RP · RD) * ID + (RP · RP) - (SR)^2.0 = 0.0;
```
# 6 - COEFFICIENT DEFINITIONS
```
QC = (RD · RD);
LC = 2.0 * (RP · RD);
CC = (RP · RP) - (SR)^2.0;
```
# 7 - QUADRATIC EQUATION DEFINITION
```
QC * (ID)^2.0 + LC * ID + CC = 0.0;
(QC * (ID)^2.0 + LC * ID + CC) / QC = 0.0;
(ID)^2.0 + (LC / QC) * ID + CC / QC = 0.0;
```
# 8 - COMPLETING THE SQUARE
```
(ID + NC)^2.0 = (ID)^2.0 + 2.0 * NC * ID + (NC)^2.0;
```
```
2.0 * NC * ID = (LC / QC) * ID;
2.0 * NC = LC / QC;
NC = LC / (2.0 * QC);
```
```
(ID)^2.0 + 2.0 * NC * ID + CC / QC = 0.0;
(ID)^2.0 + 2.0 * NC * ID = -CC / QC;
(ID + NC)^2.0 = -CC / QC + (NC)^2.0;
```
# 9 - FINAL SOLUTION
```
ID + NC = -(-CC / QC + (NC)^2.0)^0.5;
ID + NC = +(-CC / QC + (NC)^2.0)^0.5;
```
```
ID = -NC - (-CC / QC + (NC)^2.0)^0.5;
ID = -NC + (-CC / QC + (NC)^2.0)^0.5;
```
```
ID = -(LC / (2.0 * QC)) - ((LC / (2.0 * QC))^2.0 - CC / QC)^0.5;
ID = -(LC / (2.0 * QC)) + ((LC / (2.0 * QC))^2.0 - CC / QC)^0.5;
```
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
