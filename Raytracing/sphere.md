# 0 - VARIABLE DEFINITIONS

| Variable | Definition |
|:---:|:---|
| \(WP\) | World Position |
| \(RO\) | Ray Origin |
| \(RD\) | Ray Direction |
| \(ID\) | Intersection Distance |
| \(SP\) | Sphere Position |
| \(SR\) | Sphere Radius |
| \(RP\) | Relative Position |
| \(QC\) | Quadratic Coefficient |
| \(LC\) | Linear Coefficient |
| \(CC\) | Constant Coefficient |
| \(NC\) | Numeric Constant |

# 1 - RAY DEFINITION

\[
\Large
WP = RO + RD \cdot ID;
\]

# 2 - SPHERE DEFINITION

\[
\Large
(WP - SP) \cdot (WP - SP) = (SR)^{2.0};
\]

# 3 - SPHERE SUBSTITUTION

\[
\Large
WP = RO + RD \cdot ID;
\]

\[
\Large
(RO + RD \cdot ID - SP) \cdot (RO + RD \cdot ID - SP) = (SR)^{2.0};
\]

# 4 - RELATIVE VECTOR DEFINITION

\[
\Large
RP = RO - SP;
\]

\[
\Large
(RP + RD \cdot ID) \cdot (RP + RD \cdot ID) = (SR)^{2.0};
\]

# 5 - EXPANSION OF THE QUADRATIC FORM

\[
\Large
(RP \cdot RP) + 2.0 \cdot (RP \cdot RD) \cdot ID + (RD \cdot RD) \cdot (ID)^{2.0} = (SR)^{2.0};
\]

\[
\Large
(RD \cdot RD) \cdot (ID)^{2.0} + 2.0 \cdot (RP \cdot RD) \cdot ID + (RP \cdot RP) - (SR)^{2.0} = 0.0;
\]

# 6 - COEFFICIENT DEFINITIONS

\[
\Large
QC = (RD \cdot RD);
\]

\[
\Large
LC = 2.0 \cdot (RP \cdot RD);
\]

\[
\Large
CC = (RP \cdot RP) - (SR)^{2.0};
\]

# 7 - QUADRATIC EQUATION DEFINITION

\[
\Large
QC \cdot (ID)^{2.0} + LC \cdot ID + CC = 0.0;
\]

\[
\Large
\frac{QC \cdot (ID)^{2.0} + LC \cdot ID + CC}{QC} = 0.0;
\]

\[
\Large
(ID)^{2.0} + \frac{LC}{QC} \cdot ID + \frac{CC}{QC} = 0.0;
\]

# 8 - COMPLETING THE SQUARE

\[
\Large
(ID + NC)^{2.0} = (ID)^{2.0} + 2.0 \cdot NC \cdot ID + (NC)^{2.0};
\]

\[
\Large
2.0 \cdot NC \cdot ID = \frac{LC}{QC};
\]

\[
\Large
2.0 \cdot NC = \frac{LC}{QC};
\]

\[
\Large
NC = \frac{LC}{2.0 \cdot QC};
\]

\[
\Large
(ID)^{2.0} + 2.0 \cdot NC \cdot ID + \frac{CC}{QC} = 0.0;
\]

\[
\Large
(ID)^{2.0} + 2.0 \cdot NC \cdot ID = -\frac{CC}{QC};
\]

\[
\Large
(ID + NC)^{2.0} = -\frac{CC}{QC} + (NC)^{2.0};
\]

# 9 - FINAL SOLUTION

\[
\Large
ID + NC = -\left(-\frac{CC}{QC} + (NC)^{2.0}\right)^{0.5};
\]

\[
\Large
ID + NC = +\left(-\frac{CC}{QC} + (NC)^{2.0}\right)^{0.5};
\]

\[
\Large
ID = -NC - \left(-\frac{CC}{QC} + (NC)^{2.0}\right)^{0.5};
\]

\[
\Large
ID = -NC + \left(-\frac{CC}{QC} + (NC)^{2.0}\right)^{0.5};
\]

\[
\Large
ID = -\frac{LC}{2.0 \cdot QC} - \left(\left(\frac{LC}{2.0 \cdot QC}\right)^{2.0} - \frac{CC}{QC}\right)^{0.5};
\]

\[
\Large
ID = -\frac{LC}{2.0 \cdot QC} + \left(\left(\frac{LC}{2.0 \cdot QC}\right)^{2.0} - \frac{CC}{QC}\right)^{0.5};
\]

\[
\Large
ID = \frac{-LC - (LC^{2.0} - 4.0 \cdot QC \cdot CC)^{0.5}}{2.0 \cdot QC};
\]

\[
\Large
ID = \frac{-LC + (LC^{2.0} - 4.0 \cdot QC \cdot CC)^{0.5}}{2.0 \cdot QC};
\]

# SOURCE CODE

```cpp
struct source { vec3 position; vec3 direction; float minimum; float maximum; };

struct sphere { vec3 position; float radius; };

bool trySphereIntersection(const in source sampleSource, const in sphere sampleSphere, out float sample01Intersection)
{
    vec3 relativePosition = sampleSource.position - sampleSphere.position;

    float quadraticCoefficient = dot(sampleSource.direction, sampleSource.direction);
    float linearCoefficient = 2.0 * dot(relativePosition, sampleSource.direction);
    float constantCoefficient = dot(relativePosition, relativePosition) - sampleSphere.radius * sampleSphere.radius;
    float quadraticDiscriminant = linearCoefficient * linearCoefficient - 4.0 * quadraticCoefficient * constantCoefficient;

    if (quadraticDiscriminant < 0.0)
    {
        sample01Intersection = sampleSource.maximum;

        return false;
    }

    float discriminantRadical = sqrt(quadraticDiscriminant);

    float negativeIntersection = (-linearCoefficient - discriminantRadical) / (2.0 * quadraticCoefficient);
    float positiveIntersection = (-linearCoefficient + discriminantRadical) / (2.0 * quadraticCoefficient);
    float incomingIntersection = min(negativeIntersection, positiveIntersection);
    float outgoingIntersection = max(negativeIntersection, positiveIntersection);

    if (incomingIntersection >= sampleSource.minimum && incomingIntersection <= sampleSource.maximum)
    {
         sample01Intersection = incomingIntersection;

         return true;
    }

    if (outgoingIntersection >= sampleSource.minimum && outgoingIntersection <= sampleSource.maximum)
    {
        sample01Intersection = outgoingIntersection;

        return true;
    }

    return false;
}
```
