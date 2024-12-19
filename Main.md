import java.io.*;
import java.util.ArrayList;
import java.util.List;
import java.util.zip.ZipEntry;
import java.util.zip.ZipInputStream;
import java.util.zip.ZipOutputStream;

public class Main {

    public static void main(String[] args) {

        StringBuilder sb = new StringBuilder();
        // Создание каталога Games
        File games = new File("C:\\Users\\User\\Desktop\\учёба\\Games");
        if (games.mkdir()) {
            System.out.println("Папка " + games.getName() + " создана");
            sb.append(games.getAbsolutePath()).append("\n");
        }

        // Создание подкаталогов в папке Games
        File gamesSRC = new File(games + "\\src");
        if (gamesSRC.mkdir()) {
            System.out.println("Папка " + gamesSRC.getName() + " создана");
            sb.append(gamesSRC.getAbsolutePath()).append("\n");
        }
        File gamesRes = new File(games + "\\res");
        if (gamesRes.mkdir()) {
            System.out.println("Папка " + gamesRes.getName() + " создана");
            sb.append(gamesRes.getAbsolutePath()).append("\n");
        }
        File gamesSaveGames = new File(games + "\\savegames");
        if (gamesSaveGames.mkdir()) {
            System.out.println("Папка " + gamesSaveGames.getName() + " создана");
            sb.append(gamesSaveGames.getAbsolutePath()).append("\n");
        }
        File gamesTemp = new File(games + "\\temp");
        if (gamesTemp.mkdir()) {
            System.out.println("Папка " + gamesTemp.getName() + " создана");
            sb.append(gamesTemp.getAbsolutePath()).append("\n");
        }
        // Создание подкаталогов в папке src
        File srcMain = new File(gamesSRC + "\\main");
        if (srcMain.mkdir()) {
            System.out.println("Папка " + srcMain.getName() + " в подкаталоге " + gamesSRC.getName() + " создана");
            sb.append(srcMain.getAbsolutePath()).append("\n");
        }
        File srcTest = new File(gamesSRC + "\\test");
        if (srcTest.mkdir()) {
            System.out.println("Папка " + srcTest.getName() + " в подкаталоге " + gamesSRC.getName() + " создана");
            sb.append(srcTest.getAbsolutePath()).append("\n");
        }
        // Создание файлов в подкаталоге main
        File mainMain = new File(srcMain, "Main.java");
        try {
            if (mainMain.createNewFile()) {
                System.out.println("Файл " + mainMain.getName() + " создан");
                sb.append(mainMain.getAbsoluteFile()).append("\n");

            }
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
        File mainUtils = new File(srcMain, "Utils.java");
        try {
            if (mainUtils.createNewFile()) {
                System.out.println("Файл " + mainUtils.getName() + " создан");
                sb.append(mainUtils.getAbsoluteFile()).append("\n");
            }
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
        // Создание подкаталогов в папке res
        File resDraw = new File(gamesRes + "\\drawbles");
        if (resDraw.mkdir()) {
            System.out.println("Папка " + resDraw.getName() + " создана");
            sb.append(resDraw.getAbsolutePath()).append("\n");
        }
        File resVectors = new File(gamesRes + "\\vectors");
        if (resVectors.mkdir()) {
            System.out.println("Папка " + resVectors.getName() + " создана");
            sb.append(resVectors.getAbsolutePath()).append("\n");
        }
        File resIcons = new File(gamesRes + "\\icons");
        if (resIcons.mkdir()) {
            System.out.println("Папка " + resIcons.getName() + " создана");
            sb.append(resIcons.getAbsolutePath()).append("\n");
        }
        // Создание файла temp.txt
        File tempTemp = new File(gamesTemp, "temp.txt");
        try {
            if (tempTemp.createNewFile()) {
                System.out.println("Файл " + tempTemp.getName() + " создан");
                sb.append(tempTemp.getAbsoluteFile()).append("\n");
            }
        } catch (IOException e) {
            throw new RuntimeException(e);
        }

        // Cоздание лога
        try (FileWriter log = new FileWriter(tempTemp)) {
            log.write(sb.toString());
        } catch (IOException e) {
            throw new RuntimeException(e);
        }

        //Сериализация
        GameProgress progress1 = new GameProgress(1, 1, 1, 1);
        GameProgress progress2 = new GameProgress(2, 2, 2, 2);
        GameProgress progress3 = new GameProgress(3, 3, 3, 3);

        saveGame(gamesSaveGames + "\\save1.dat", progress1);
        saveGame(gamesSaveGames + "\\save2.dat", progress2);
        saveGame(gamesSaveGames + "\\save3.dat", progress3);

        List<String> zipList = new ArrayList<>();
        zipList.add(gamesSaveGames + "\\save1.dat");
        zipList.add(gamesSaveGames + "\\save2.dat");
        zipList.add(gamesSaveGames + "\\save3.dat");

        zipFiles("C:\\Users\\User\\Desktop\\учёба\\Games\\savegames\\zip.zip", zipList);
        openZip("C:\\Users\\User\\Desktop\\учёба\\Games\\savegames\\zip.zip", "C:\\Users\\User\\Desktop\\учёба\\Games\\savegames");

        System.out.println(openProgress("C:\\Users\\User\\Desktop\\учёба\\Games\\savegames\\save1.dat"));

    }

    public static void saveGame(String saveFile, GameProgress progressObject) {
        try (
                FileOutputStream fosSave = new FileOutputStream(saveFile);
                ObjectOutputStream oosSave = new ObjectOutputStream(fosSave)
        ) {
            oosSave.writeObject(progressObject);

        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
    }


    public static void zipFiles(String zipPath, List<String> filesToZipPath) {
        try (ZipOutputStream zipoutSave = new ZipOutputStream(new FileOutputStream(zipPath))) {
            for (String i : filesToZipPath) {
                try (FileInputStream fisSave = new FileInputStream(i)) {
                    ZipEntry entry = new ZipEntry("save" + filesToZipPath.indexOf(i) + ".dat");
                    zipoutSave.putNextEntry(entry);
                    byte[] buffer = new byte[fisSave.available()];
                    fisSave.read(buffer);
                    zipoutSave.write(buffer);
                    zipoutSave.closeEntry();
                } catch (Exception ex) {
                    System.out.println(ex.getMessage());
                }
            }
        } catch (Exception ex) {
            System.out.println(ex.getMessage());
        }
    }

    public static void openZip(String unzipFilePath, String unzipPath) {
        try (ZipInputStream zis = new ZipInputStream(new FileInputStream(unzipFilePath))) {
            ZipEntry entry;

            while ((entry = zis.getNextEntry()) != null) {
                unzipPath = entry.getName();
                FileOutputStream fos = new FileOutputStream(unzipPath);
                for (int c = zis.read(); c != -1; c = zis.read()) {
                    fos.write(c);
                }
                fos.flush();
                zis.closeEntry();
                fos.close();
            }

        } catch (IOException ex) {
            System.out.println(ex.getMessage());
        }
    }

    public static GameProgress openProgress(String savePath) {
        GameProgress gameProgress = null;
        try (FileInputStream fis = new FileInputStream(savePath);
             ObjectInputStream ois = new ObjectInputStream(fis)) {
            gameProgress = (GameProgress) ois.readObject();
        } catch (Exception ex) {
            System.out.println(ex.getMessage());
        }
        return gameProgress;
    }

}

