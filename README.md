# Speech-Emotion-detection-task
//speech emotion detection
import javax.sound.sampled.*;
import java.io.File;
import java.io.IOException;
import java.util.Arrays;

public class SpeechEmotionDetection {

    public static void main(String[] args) {
        String audioFilePath = "path/to/audio/file.wav";
        double[] audioData = readAudioFile(audioFilePath);
        double[] features = extractFeatures(audioData);
        String emotion = classifyEmotion(features);
        System.out.println("Detected Emotion: " + emotion);
    }

    private static double[] readAudioFile(String filePath) {
        try {
            AudioInputStream audioInputStream = AudioSystem.getAudioInputStream(new File(filePath));
            AudioFormat format = audioInputStream.getFormat();
            byte[] bytes = new byte[(int) audioInputStream.getFrameLength() * format.getFrameSize()];
            audioInputStream.read(bytes);
            return convertBytesToDoubles(bytes);
        } catch (UnsupportedAudioFileException | IOException e) {
            e.printStackTrace();
            return new double[0];
        }
    }

    private static double[] convertBytesToDoubles(byte[] bytes) {
        double[] doubles = new double[bytes.length / 2];
        for (int i = 0; i < doubles.length; i++) {
            doubles[i] = ((bytes[2 * i] & 0xFF) | (bytes[2 * i + 1] << 8)) / 32768.0; // Normalize to [-1, 1]
        }
        return doubles;
    }

    private static double[] extractFeatures(double[] audioData) {
        double mean = Arrays.stream(audioData).average().orElse(0);
        double stdDev = Math.sqrt(Arrays.stream(audioData).map(x -> Math.pow(x - mean, 2)).average().orElse(0));
        return new double[]{mean, stdDev};
    }

    private static String classifyEmotion(double[] features) {
        if (features[0] > 0.1 && features[1] < 0.2) {
            return "Happiness";
        } else if (features[0] < -0.1 && features[1] > 0.2) {
            return "Sadness";
        } else if (features[0] < -0.1 && features[1] < 0.2) {
            return "Anger";
        } else {
            return "Neutral";
        }
    }
}


