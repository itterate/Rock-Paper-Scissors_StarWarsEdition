public class AnimatedCircle {
    double rx;
    double ry;
    double radius;
    String name;
    // AudioClip sound;

    // Создаю класс шариков с радиусом, и с случайными координатами.
    public AnimatedCircle(double radius) {
        this.radius = radius;
        this.rx = 2 * radius + Math.random() * (2 - 4 * radius);
        this.ry = 2 * radius + Math.random() * (2 - 4 * radius);
        // случайным образом идентифицирию каждый элемент
        int k = (int) (3 * Math.random() + 1);
        if (k == 1) {
            this.name = "endor";
            StdDraw.picture(rx, ry, "endor.png");

        }
        else if (k == 2) {
            this.name = "death_star";
            StdDraw.picture(rx, ry, "death_star.png");

        }
        else {
            this.name = "star_destroyer";
            StdDraw.picture(rx, ry, "star_destroyer.png");

            // URL soundUrl = getClass().getResource("sound.wav");
            //

        }
    }

    // даю случайные значение скоростей и направление
    public static double setV() {
        double k = 0.01;
        double sign = Math.random();
        if (sign < 0.5) {
            k *= -1;
        }
        return k * Math.random();
    }

    // каждый элемент содержит свой цвет ну или картиинку
    // endor beats death_star, death_star beats star_destroyer, star_destroyer beats endor
    public static void setColor(double rx, double ry, String name) {
        if (name.equals("endor")) {
            StdDraw.setPenColor(StdDraw.BOOK_LIGHT_BLUE);
            StdDraw.picture(rx, ry, "endor.png");
        }
        if (name.equals("death_star")) {
            StdDraw.setPenColor(StdDraw.ORANGE);
            StdDraw.picture(rx, ry, "death_star.png");
        }
        if (name.equals("star_destroyer")) {
            StdDraw.setPenColor(StdDraw.RED);
            StdDraw.picture(rx, ry, "star_destroyer.png");
        }

    }


    public static void main(String[] args) {
        // рандомное количество шариков
        int n = (int) (20 * Math.random()) + 6;
        AnimatedCircle[] circles = new AnimatedCircle[n];
        double radius = 0.05;
        // Вообщем я создал массив класса animatedCircle где я разместил координаты каждого шарика.
        for (int i = 0; i < n; i++) {
            circles[i] = new AnimatedCircle(radius);
        }

        double[] velocitiesX = new double[n];
        double[] velocitiesY = new double[n];
        for (int i = 0; i < n; i++) {
            velocitiesX[i] = setV();
            velocitiesY[i] = setV();
        }

        StdDraw.setXscale(0, +2);
        StdDraw.setYscale(0, +2);
        StdAudio.playInBackground("star-wars.wav");
        // В этом бесконечным цикле частицы сталкиваются с друг-другом
        while (true) {
            StdDraw.setPenColor(StdDraw.BLACK);
            StdDraw.filledSquare(1, 1, 1.0);

            for (int i = 0; i < n; i++) {

                AnimatedCircle currentCircle = circles[i];
                // случай когда шар сталкивается со стенкой
                if (currentCircle.rx - radius + velocitiesX[i] <= 0
                        || currentCircle.rx + radius + velocitiesX[i] >= 2) {
                    velocitiesX[i] = -velocitiesX[i];
                }
                if (currentCircle.ry - radius + velocitiesY[i] <= 0
                        || currentCircle.ry + radius + velocitiesY[i] >= 2) {
                    velocitiesY[i] = -velocitiesY[i];
                }

                // случай когда шарики сталкиваются между собой
                for (int j = i + 1; j < n; j++) {
                    AnimatedCircle otherCircle = circles[j];
                    double distanceX = currentCircle.rx - otherCircle.rx;
                    double distanceY = currentCircle.ry - otherCircle.ry;
                    double distance = Math.sqrt(distanceX * distanceX + distanceY * distanceY);
                    double sin = distanceX / distance;
                    double cos = distanceY / distance;

                    if (distance <= 2 * radius) {

                        if (currentCircle.name.equals("endor")) {
                            if (otherCircle.name.equals("star_destroyer")) {
                                currentCircle.name = "star_destroyer";
                            }
                            if (otherCircle.name.equals("death_star")) {
                                otherCircle.name = "endor";
                            }
                        }

                        if (currentCircle.name.equals("death_star")) {
                            if (otherCircle.name.equals("endor")) {
                                currentCircle.name = "endor";
                            }
                            if (otherCircle.name.equals("star_destroyer")) {
                                otherCircle.name = "death_star";
                            }
                        }

                        if (currentCircle.name.equals("star_destroyer")) {
                            if (otherCircle.name.equals("death_star")) {
                                currentCircle.name = "death_star";
                            }
                            if (otherCircle.name.equals("endor")) {
                                otherCircle.name = "star_destroyer";
                            }
                        }
                        // шарики сталкиваются и отскакивают в разные стороны
                        // значение общего вектора скорости
                        double Dvector = (velocitiesX[i] - velocitiesX[j]) * cos +
                                (velocitiesY[i] - velocitiesY[j]) * sin;
                        // их компоненты по Х и У осям
                        double dvx = Dvector * cos;
                        double dvy = Dvector * sin;
                        // скорости шариков.
                        velocitiesX[i] -= dvx;
                        velocitiesY[i] -= dvy;

                        velocitiesX[j] += dvx;
                        velocitiesY[j] += dvy;

                    }
                }

                currentCircle.rx += velocitiesX[i];
                currentCircle.ry += velocitiesY[i];


                setColor(currentCircle.rx, currentCircle.ry, currentCircle.name);

                // StdDraw.circle(currentCircle.rx, currentCircle.ry, radius);
            }

            StdDraw.show(20);

        }

    }
}
